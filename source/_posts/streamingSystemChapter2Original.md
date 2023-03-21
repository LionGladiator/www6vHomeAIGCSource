---
title: 《Streaming System》-Chapter 2. The What, Where, When, and How of Data Processing
date: 2000-03-17 22:08:13
tags: 
  - Streaming System
categories: 
  - Streaming System
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->



{% details 点击  原文  %}

# **Roadmap**

To help set the stage for this chapter, I want to lay out the five main concepts

that will underpin all of the discussions therein, and really, for most of the rest

of Part I. We’ve already covered two of them.

In Chapter 1, I first established the critical distinction between event time (the

time that events happen) and processing time (the time they are observed

during processing). This provides the foundation for one of the main theses

put forth in this book: if you care about both correctness and the context

within which events actually occurred, you must analyze data relative to their

inherent event times, not the processing time at which they are encountered

during the analysis itself.

I then introduced the concept of *windowing* (i.e., partitioning a dataset along

temporal boundaries), which is a common approach used to cope with the fact

that unbounded data sources technically might never end. Some simpler

examples of windowing strategies are *fixed* and *sliding* windows, but more

sophisticated types of windowing, such as *sessions* (in which the windows are

defined by features of the data themselves; for example, capturing a session of

activity per user followed by a gap of inactivity) also see broad usage.

In addition to these two concepts, we’re now going to look closely at three

more:

- Triggers

A trigger is a mechanism for declaring when the output for a window

should be materialized relative to some external signal. Triggers provide

flexibility in choosing when outputs should be emitted. In some sense,

you can think of them as a flow control mechanism for dictating when

results should be materialized. Another way of looking at it is that triggers

are like the shutter-release on a camera, allowing you to declare when to

take a snapshots in time of the results being computed.

Triggers also make it possible to observe the output for a window multiple

times as it evolves. This in turn opens up the door to refining results over

time, which allows for providing speculative results as data arrive, as well

as dealing with changes in upstream data (revisions) over time or data that

arrive late (e.g., mobile scenarios, in which someone’s phone records

various actions and their event times while the person is offline and then

proceeds to upload those events for processing upon regaining

connectivity).

- Watermarks

A watermark is a notion of input completeness with respect to event

times. A watermark with value of time *X* makes the statement: “all input

data with event times less than *X* have been observed.” As such,

watermarks act as a metric of progress when observing an unbounded data

source with no known end. We touch upon the basics of watermarks in

this chapter, and then Slava goes super deep on the subject in Chapter 3.

- Accumulation

An accumulation mode specifies the relationship between multiple results

that are observed for the same window. Those results might be completely

disjointed; that is, representing independent deltas over time, or there

might be overlap between them. Different accumulation modes have

different semantics and costs associated with them and thus find

applicability across a variety of use cases.

Also, because I think it makes it easier to understand the relationships

between all of these concepts, we revisit the old and explore the new within

the structure of answering four questions, all of which I propose are critical to

every unbounded data processing problem:

- *What* results are calculated? This question is answered by the types

of transformations within the pipeline. This includes things like

computing sums, building histograms, training machine learning

models, and so on. It’s also essentially the question answered by

classic batch processing

- *Where* in event time are results calculated? This question is answered

by the use of event-time windowing within the pipeline. This

includes the common examples of windowing from Chapter 1 (fixed,

sliding, and sessions); use cases that seem to have no notion of

windowing (e.g., time-agnostic processing; classic batch processing

also generally falls into this category); and other, more complex

types of windowing, such as time-limited auctions. Also note that it

can include processing-time windowing, as well, if you assign

ingress times as event times for records as they arrive at the system.

- *When* in processing time are results materialized? This question is

answered by the use of triggers and (optionally) watermarks. There

are infinite variations on this theme, but the most common patterns

are those involving repeated updates (i.e., materialized view

semantics), those that utilize a watermark to provide a single output

per window only after the corresponding input is believed to be

complete (i.e., classic batch processing semantics applied on a per

window basis), or some combination of the two.

- *How* do refinements of results relate? This question is answered by

the type of accumulation used: discarding (in which results are all

independent and distinct), accumulating (in which later results build

upon prior ones), or accumulating and retracting (in which both the

accumulating value plus a retraction for the previously triggered

value(s) are emitted).

We look at each of these questions in much more detail throughout the rest of

the book. And, yes, I’m going to run this color scheme thing into the ground

in an attempt to make it abundantly clear which concepts relate to which

question in the *What*/*Where*/*When*/*How* idiom. You’re welcome <winky

smiley/>.

{%  enddetails   %}





{% details 点击  原文  %}

# **Batch Foundations: \*What\* and \*Where\***

Okay, let’s get this party started. First stop: batch processing.

### ***What\*: Transformations**

The transformations applied in classic batch processing answer the question:

“*What* results are calculated?” Even though you are likely already familiar

with classic batch processing, we’re going to start there anyway because it’s

the foundation on top of which we add all of the other concepts.

In the rest of this chapter (and indeed, through much of the book), we look at

a single example: computing keyed integer sums over a simple dataset

consisting of nine values. Let’s imagine that we’ve written a team-based

mobile game and we want to build a pipeline that calculates team scores by

summing up the individual scores reported by users’ phones. If we were to

capture our nine example scores in a SQL table named “UserScores,” it might

look something like this:

------

| Name | Team | Score | EventTime | ProcTime |

------

| Julie | TeamX | 5 | 12:00:26 | 12:05:19 |

| Frank | TeamX | 9 | 12:01:26 | 12:08:19 |

| Ed | TeamX | 7 | 12:02:26 | 12:05:39 |

| Julie | TeamX | 8 | 12:03:06 | 12:07:06 |

| Amy | TeamX | 3 | 12:03:39 | 12:06:13 |

| Fred | TeamX | 4 | 12:04:19 | 12:06:39 |

| Naomi | TeamX | 3 | 12:06:39 | 12:07:19 |

| Becky | TeamX | 8 | 12:07:26 | 12:08:39 |

| Naomi | TeamX | 1 | 12:07:46 | 12:09:00 |

------

Note that all the scores in this example are from users on the same team; this

is to keep the example simple, given that we have a limited number of

dimensions in our diagrams that follow. And because we’re grouping by

team, we really just care about the last three columns:

- Score

The individual user score associated with this event

- EventTime

The event time for the score; that is, the time at which the score occurred

- ProcTime

The processing for the score; that is, the time at which the score was

observed by the pipeline

For each example pipeline, we’ll look at a time-lapse diagram that highlights

how the data evolves over time. Those diagrams plot our nine scores in the

two dimensions of time we care about: event time in the x-axis, and

processing time in the y-axis. Figure 2-1 illustrates what a static plot of the

input data looks like.

*Figure 2-1. Nine input records, plotted in both event time and processing time*

Subsequent time-lapse diagrams are either animations (Safari) or a sequence

of frames (print and all other digital formats), allowing you to see how the

data are processed over time (more on this shortly after we get to the first

time-lapse diagram).

Preceding each example is a short snippet of Apache Beam Java SDK

pseudocode to make the definition of the pipeline more concrete. It is

pseudocode in the sense that I sometime bend the rules to make the examples

clearer, elide details (like the use of concrete I/O sources), or simplify names

(the trigger names in Beam Java 2.x and earlier are painfully verbose; I use

simpler names for clarity). Beyond minor things like those, it’s otherwise

real-world Beam code (and real code is available on GitHub for all examples

in this chapter).

If you’re already familiar with something like Spark or Flink, you should

have a relatively easy time understanding what the Beam code is doing. But

to give you a crash course in things, there are two basic primitives in Beam:

- PCollections

These represent datasets (possibly massive ones) across which parallel

transformations can be performed (hence the “P” at the beginning of the

name).

- PTransforms

These are applied to PCollections to create new PCollections.

PTransforms may perform element-wise transformations, they may

group/aggregate multiple elements together, or they may be a composite

combination of other PTransforms, as depicted in Figure 2-2.

*Figure 2-2. Types of transformations*

For the purposes of our examples, we typically assume that we start out with

a pre-loaded PCollection<KV<Team, Integer>> named “input” (that is, a

PCollection composed of key/value pairs of Teams and Integers, where

the Teams are just something like Strings representing team names, and the

Integers are scores from any individual on the corresponding team). In a

real-world pipeline, we would’ve acquired input by reading in a

PCollection<String> of raw data (e.g., log records) from an I/O source and

then transforming it into a PCollection<KV<Team, Integer>> by parsing

the log records into appropriate key/value pairs. For the sake of clarity in this

first example, I include pseudocode for all of those steps, but in subsequent

examples, I elide the I/O and parsing.

Thus, for a pipeline that simply reads in data from an I/O source, parses

team/score pairs, and calculates per-team sums of scores, we’d have

something like that shown in Example 2-1.

*Example 2-1. Summation pipeline*

```
PCollection<String> raw = IO.read(...);
PCollection<KV<Team, Integer>> input = raw.apply(new ParseFn());
PCollection<KV<Team, Integer>> totals =
input.apply(Sum.integersPerKey());
```

Key/value data are read from an I/O source, with a Team (e.g., String of the

team name) as the key and an Integer (e.g., individual team member scores)

as the value. The values for each key are then summed together to generate

per-key sums (e.g., total team score) in the output collection.

For all the examples to come, after seeing a code snippet describing the

pipeline that we’re analyzing, we’ll then look at a time-lapse diagram

showing the execution of that pipeline over our concrete dataset for a single

key. In a real pipeline, you can imagine that similar operations would be

happening in parallel across multiple machines, but for the sake of our

examples, it will be clearer to keep things simple.

As noted previously, Safari editions present the complete execution as an

animated movie, whereas print and all other digital formats use a static

sequence of key frames that provide a sense of how the pipeline progresses

over time. In both cases, we also provide a URL to a fully animated version

on *[www.streamingbook.net](http://www.streamingbook.net)*.

Each diagram plots the inputs and outputs across two dimensions: event time

(on the x-axis) and processing time (on the y-axis). Thus, real time as

observed by the pipeline progresses from bottom to top, as indicated by the

thick horizontal black line that ascends in the processing-time axis as time

progresses. Inputs are circles, with the number inside the circle representing

the value of that specific record. They start out light gray, and darken as the

pipeline observes them.

As the pipeline observes values, it accumulates them in its intermediate state

and eventually materializes the aggregate results as output. State and output

are represented by rectangles (gray for state, blue for output), with the

aggregate value near the top, and with the area covered by the rectangle

representing the portions of event time and processing time accumulated into

the result. For the pipeline in Example 2-1, it would look something like that

shown in Figure 2-3 when executed on a classic batch engine.

*Figure 2-3. Classic batch processing*

Because this is a batch pipeline, it accumulates state until it’s seen all of the

inputs (represented by the dashed green line at the top), at which point it

produces its single output of 48. In this example, we’re calculating a sum over

all of event time because we haven’t applied any specific windowing

transformations; hence the rectangles for state and output cover the entirety of

the x-axis. If we want to process an unbounded data source, however, classic

batch processing won’t be sufficient; we can’t wait for the input to end,

because it effectively never will. One of the concepts we want is windowing,

which we introduced in Chapter 1. Thus, within the context of our second

question—“*Where* in event time are results calculated?”—we’ll now briefly

revisit windowing.

### ***Where\*: Windowing**

As discussed in Chapter 1, windowing is the process of slicing up a data

source along temporal boundaries. Common windowing strategies include

fixed windows, sliding windows, and sessions windows, as demonstrated in

Figure 2-4.

*Figure 2-4. Example windowing strategies. Each example is shown for three different keys, highlighting*

*the difference between aligned windows (which apply across all the data) and unaligned windows*

*(which apply across a subset of the data).*

To get a better sense of what windowing looks like in practice, let’s take our

integer summation pipeline and window it into fixed, two-minute windows.

With Beam, the change is a simple addition of a Window.into transform,

which you can see highlighted in Example 2-2.

*Example 2-2. Windowed summation code*

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES)))
.apply(Sum.integersPerKey());
```

Recall that Beam provides a unified model that works in both batch and

streaming because semantically batch is really just a subset of streaming. As

such, let’s first execute this pipeline on a batch engine; the mechanics are

more straightforward, and it will give us something to directly compare

against when we switch to a streaming engine. Figure 2-5 presents the result.

*Figure 2-5. Windowed summation on a batch engine*

As before, inputs are accumulated in state until they are entirely consumed,

after which output is produced. In this case, however, instead of one output,

we get four: a single output, for each of the four relevant two-minute event

time windows.

At this point we’ve revisited the two main concepts that I introduced in

Chapter 1: the relationship between the event-time and processing-time

domains, and windowing. If we want to go any further, we’ll need to start

adding the new concepts mentioned at the beginning of this section: triggers,

watermarks, and accumulation.

{%  enddetails   %}





{% details 点击  原文  %}

# **Going Streaming: *When* and *How***

We just observed the execution of a windowed pipeline on a batch engine.

But, ideally, we’d like to have lower latency for our results, and we’d also

like to natively handle unbounded data sources. Switching to a streaming

engine is a step in the right direction, but our previous strategy of waiting

until our input has been consumed in its entirety to generate output is no

longer feasible. Enter triggers and watermarks.

### ***When*: The Wonderful Thing About Triggers Is Triggers**

### **Are Wonderful Things!**

Triggers provide the answer to the question: “*When* in processing time are

results materialized?” Triggers declare when output for a window should

happen in processing time (though the triggers themselves might make those

decisions based on things that happen in other time domains, such as

watermarks progressing in the event-time domain, as we’ll see in a few

moments). Each specific output for a window is referred to as a *pane* of the

window.

Though it’s possible to imagine quite a breadth of possible triggering

semantics, conceptually there are only two generally useful types of triggers,

and practical applications almost always boil down using either one or a

combination of both:

- Repeated update triggers

These periodically generate updated panes for a window as its contents

evolve. These updates can be materialized with every new record, or they

can happen after some processing-time delay, such as once a minute. The

choice of period for a repeated update trigger is primarily an exercise in

balancing latency and cost.

- Completeness triggers

These materialize a pane for a window only after the input for that

window is believed to be complete to some threshold. This type of trigger

is most analogous to what we’re familiar with in batch processing: only

after the input is complete do we provide a result. The difference in the

trigger-based approach is that the notion of completeness is scoped to the

context of a single window, rather than always being bound to the

completeness of the entire input.

Repeated update triggers are the most common type of trigger encountered in

streaming systems. They are simple to implement and simple to understand,

and they provide useful semantics for a specific type of use case: repeated

(and eventually consistent) updates to a materialized dataset, analogous to the

semantics you get with materialized views in the database world.

Completeness triggers are less frequently encountered, but provide streaming

semantics that more closely align with those from the classic batch processing

world. They also provide tools for reasoning about things like missing data

and late data, both of which we discuss shortly (and in the next chapter) as we

explore the underlying primitive that drives completeness triggers:

watermarks.

But first, let’s start simple and look at some basic repeated update triggers in

action. To make the notion of triggers a bit more concrete, let’s go ahead and

add the most straightforward type of trigger to our example pipeline: a trigger

that fires with every new record, as shown in Example 2-3.

*Example 2-3. Triggering repeatedly with every record*

`PCollection<KV<Team, Integer>> totals = input`

`.apply(Window.into(FixedWindows.of(TWO_MINUTES))`

`.triggering(Repeatedly(AfterCount(1))));`

`.apply(Sum.integersPerKey());`

If we were to run this new pipeline on a streaming engine, the results would

look something like that shown in Figure 2-6.

*Figure 2-6. Per-record triggering on a streaming engine*

You can see how we now get multiple outputs (panes) for each window: once

per corresponding input. This sort of triggering pattern works well when the

output stream is being written to some sort of table that you can simply poll

for results. Any time you look in the table, you’ll see the most up-to-date

value for a given window, and those values will converge toward correctness

over time.

One downside of per-record triggering is that it’s quite chatty. When

processing large-scale data, aggregations like summation provide a nice

opportunity to reduce the cardinality of the stream without losing information.

This is particularly noticeable for cases in which you have high-volume keys;

for our example, massive teams with lots of active players. Imagine a

massively multiplayer game in which players are split into one of two

factions, and you want to tally stats on a per-faction basis. It’s probably

unnecessary to update your tallies with every new input record for every

player in a given faction. Instead, you might be happy updating them after

some processing-time delay, say every second, or every minute. The nice side

effect of using processing-time delays is that it has an equalizing effect across

high-volume keys or windows: the resulting stream ends up being more

uniform cardinality-wise.

There are two different approaches to processing-time delays in triggers:

*aligned delays* (where the delay slices up processing time into fixed regions

that align across keys and windows) and *unaligned delays* (where the delay is

relative to the data observed within a given window). A pipeline with

unaligned delays might look like Example 2-4, the results of which are shown

in Figure 2-7.

*Example 2-4. Triggering on aligned two-minute processing-time boundaries*

`PCollection<KV<Team, Integer>> totals = input`

`.apply(Window.into(FixedWindows.of(TWO_MINUTES))`

`.triggering(Repeatedly(AlignedDelay(TWO_MINUTES)))`

`.apply(Sum.integersPerKey());`

*Figure 2-7. Two-minute aligned delay triggers (i.e., microbatching)*

This sort of aligned delay trigger is effectively what you get from a

microbatch streaming system like Spark Streaming. The nice thing about it is

predictability; you get regular updates across all modified windows at the

same time. That’s also the downside: all updates happen at once, which

results in bursty workloads that often require greater peak provisioning to

properly handle the load. The alternative is to use an unaligned delay. That

would look something Example 2-5 in Beam. Figure 2-8 presents the results.

*Example 2-5. Triggering on unaligned two-minute processing-time*

*boundaries*

`PCollection<KV<Team, Integer>> totals = input`

`.apply(Window.into(FixedWindows.of(TWO_MINUTES))`

`.triggering(Repeatedly(UnalignedDelay(TWO_MINUTES))`

`.apply(Sum.integersPerKey());`

*Figure 2-8. Two-minute unaligned delay triggers*

Contrasting the unaligned delays in Figure 2-8 to the aligned delays in

Figure 2-6, it’s easy to see how the unaligned delays spread the load out more

evenly across time. The actual latencies involved for any given window differ

between the two, sometimes more and sometimes less, but in the end the

average latency will remain essentially the same. From that perspective,

unaligned delays are typically the better choice for large-scale processing

because they result in a more even load distribution over time.

Repeated update triggers are great for use cases in which we simply want

periodic updates to our results over time and are fine with those updates

converging toward correctness with no clear indication of when correctness is

achieved. However, as we discussed in Chapter 1, the vagaries of distributed

systems often lead to a varying level of skew between the time an event

happens and the time it’s actually observed by your pipeline, which means it

can be difficult to reason about when your output presents an accurate and

complete view of your input data. For cases in which input completeness

matters, it’s important to have some way of reasoning about completeness

rather than blindly trusting the results calculated by whichever subset of data

happen to have found their way to your pipeline. Enter watermarks.



{%  enddetails   %}





{% details 点击  原文  %}

### ***When\*: Watermarks**

Watermarks are a supporting aspect of the answer to the question: “*When* in

processing time are results materialized?” Watermarks are temporal notions

of input completeness in the event-time domain. Worded differently, they are

the way the system measures progress and completeness relative to the event

times of the records being processed in a stream of events (either bounded or

unbounded, though their usefulness is more apparent in the unbounded case).

Recall this diagram from Chapter 1, slightly modified in Figure 2-9, in which

I described the skew between event time and processing time as an ever

changing function of time for most real-world distributed data processing

systems.

*Figure 2-9. Event-time progress, skew, and watermarks*

That meandering red line that I claimed represented reality is essentially the

watermark; it captures the progress of event-time completeness as processing

time progresses. Conceptually, you can think of the watermark as a function,

*F*(*P*) → *E*, which takes a point in processing time and returns a point in event

time. That point in event time, *E*, is the point up to which the system believes

all inputs with event times less than *E* have been observed. In other words,

it’s an assertion that no more data with event times less than *E* will ever be

seen again. Depending upon the type of watermark, perfect or heuristic, that

assertion can be a strict guarantee or an educated guess, respectively:

- Perfect watermarks

For the case in which we have perfect knowledge of all of the input data,

it’s possible to construct a perfect watermark. In such a case, there is no

such thing as late data; all data are early or on time.

- Heuristic watermarks

For many distributed input sources, perfect knowledge of the input data is

impractical, in which case the next best option is to provide a heuristic

watermark. Heuristic watermarks use whatever information is available

about the inputs (partitions, ordering within partitions if any, growth rates

of files, etc.) to provide an estimate of progress that is as accurate as

possible. In many cases, such watermarks can be remarkably accurate in

their predictions. Even so, the use of a heuristic watermark means that it

might sometimes be wrong, which will lead to late data. We show you

about ways to deal with late data soon.

Because they provide a notion of completeness relative to our inputs,

watermarks form the foundation for the second type of trigger mentioned

previously: *completeness triggers*. Watermarks themselves are a fascinating

and complex topic, as you’ll see when you get to Slava’s watermarks deep

dive in Chapter 3. But for now, let’s look at them in action by updating our

example pipeline to utilize a completeness trigger built upon watermarks, as

demonstrated in Example 2-6.

*Example 2-6. Watermark completeness trigger*

```
PCollection<KV<Team, Integer>> totals = input
.apply(Window.into(FixedWindows.of(TWO_MINUTES))
.triggering(AfterWatermark()))
.apply(Sum.integersPerKey());
```

Now, an interesting quality of watermarks is that they are a class of functions,

meaning there are multiple different functions *F*(*P*) → *E* that satisfy the

properties of a watermark, to varying degrees of success. As I noted earlier,

for situations in which you have perfect knowledge of your input data, it

might be possible to build a perfect watermark, which is the ideal situation.

But for cases in which you lack perfect knowledge of the inputs or for which

it’s simply too computationally expensive to calculate the perfect watermark,

you might instead choose to utilize a heuristic for defining your watermark.

The point I want to make here is that the given watermark algorithm in use is

independent from the pipeline itself. We’re not going to discuss in detail what

it means to implement a watermark here (Slava does that in Chapter 3). For

now, to help drive home this idea that a given input set can have different

watermarks applied to it, let’s take a look at our pipeline in Example 2-6

when executed on the same dataset but using two distinct watermark

implementations (Figure 2-10): on the left, a perfect watermark; on the right,

a heuristic watermark.

In both cases, windows are materialized as the watermark passes the end of

the window. The perfect watermark, as you might expect, perfectly captures

the event-time completeness of the pipeline as time progresses. In contrast,

the specific algorithm used for the heuristic watermark on the right fails to

take the value of 9 into account, which drastically changes the shape of the

materialized outputs, both in terms of output latency and correctness (as seen

by the incorrect answer of 5 that’s provided for the [12:00, 12:02) window).

The big difference between the watermark triggers from Figure 2-9 and the

repeated update triggers we saw in Figures 2-5 through 2-7 is that the

*watermarks give us a way to reason about the completeness of our input*.

Until the system materializes an output for a given window, we know that the

system does not yet believe the inputs to be complete. This is especially

important for use cases in which you want to reason about a *lack of data* in

the input, or *missing data*.

*Figure 2-10. Windowed summation on a streaming engine with perfect (left) and heuristic (right)*

*watermarks*

A great example of a missing-data use case is outer joins. Without a notion of

completeness like watermarks, how do you know when to give up and emit a

partial join rather than continue to wait for that join to complete? You don’t.

And basing that decision on a processing-time delay, which is the common

approach in streaming systems that lack true watermark support, is not a safe

way to go, because of the variable nature of event-time skew we spoke about

in Chapter 1: as long as skew remains smaller than the chosen processing

time delay, your missing-data results will be correct, but any time skew grows

beyond that delay, they will suddenly become *in*correct. From this

perspective, event-time watermarks are a critical piece of the puzzle for many

real-world streaming use cases which must reason about a lack of data in the

input, such as outer joins, anomaly detection, and so on.

Now, with that said, these watermark examples also highlight two

*shortcomings* of watermarks (and any other notion of completeness),

specifically that they can be one of the following:

- Too slow

When a watermark of any type is correctly delayed due to known

unprocessed data (e.g., slowly growing input logs due to network

bandwidth constraints), that translates directly into delays in output if

advancement of the watermark is the only thing you depend on for

stimulating results.

This is most obvious in the left diagram of Figure 2-10, for which the late

arriving 9 holds back the watermark for all the subsequent windows, even

though the input data for those windows become complete earlier. This is

particularly apparent for the second window, [12:02, 12:04), for which it

takes nearly seven minutes from the time the first value in the window

occurs until we see any results for the window whatsoever. The heuristic

watermark in this example doesn’t suffer the same issue quite so badly

(five minutes until output), but don’t take that to mean heuristic

watermarks never suffer from watermark lag; that’s really just a

consequence of the record I chose to omit from the heuristic watermark in

this specific example.

The important point here is the following: Although watermarks provide a

very useful notion of completeness, depending upon completeness for

producing output is often not ideal from a latency perspective. Imagine a

dashboard that contains valuable metrics, windowed by hour or day. It’s

unlikely you’d want to wait a full hour or day to begin seeing results for

the current window; that’s one of the pain points of using classic batch

systems to power such systems. Instead, it would be much nicer to see the

results for those windows refine over time as the inputs evolve and

eventually become complete.

- Too fast

When a heuristic watermark is incorrectly advanced earlier than it should

be, it’s possible for data with event times before the watermark to arrive

some time later, creating late data. This is what happened in the example

on the right: the watermark advanced past the end of the first window

before all the input data for that window had been observed, resulting in

an incorrect output value of 5 instead of 14. This shortcoming is strictly a

problem with heuristic watermarks; their heuristic nature implies they will

sometimes be wrong. As a result, relying on them alone for determining

when to materialize output is insufficient if you care about correctness.

In Chapter 1, I made some rather emphatic statements about notions of

completeness being insufficient for most use cases requiring robust out-of-

order processing of unbounded data streams. These two shortcomings—

watermarks being too slow or too fast—are the foundations for those

arguments. You simply cannot get both low latency and correctness out of a

system that relies solely on notions of completeness. So, for cases for which

you do want the best of both worlds, what’s a person to do? Well, if repeated

update triggers provide low-latency updates but no way to reason about

completeness, and watermarks provide a notion of completeness but variable

and possible high latency, why not combine their powers together?



{%  enddetails   %}