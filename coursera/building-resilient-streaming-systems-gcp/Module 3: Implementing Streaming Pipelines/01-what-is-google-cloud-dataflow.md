## Element-wise stream processing is easy. Aggregation and composite on unbounded data is hard
So when you look at processing of data in a stream, if all you needed to do was to process the data element by element, that's easy, right. Some element comes in, you carry out some processing and then you send it out. That's very easy. If every element is independent, that's not a problem. However, if you need to combine elements, if you need to do aggregations, if you need to basically say take two elements and they're, because they both have the same key, I need to basically take them, aggregate them, computer, some computer mean, computer list of those things. Now it becomes a little bit harder. And we'll talk about why that is. Of course, the hardest is if you need to do a compositing. If you have data that's, that's coming in and they may have different keys and you need to do sophisticated processing in terms of processing data that come in from two different sources, and need to composite them, add, you know, add information to the data from one source using data computed or computations based on another source, compositing. That's even harder. But we need to be able to do aggregations and compositing on stream data. So the state of the art, if you wanted to do that until very recently, was that you would write two sets of code. You would write one set of code that handled batch data and the idea was that you were very focused on having this as accurate as possible. And then you'd build another set of code that processed streaming data, data that came in. And the assumption was that because data are coming in in a stream you cannot guarantee accuracy. There's going to be latency, there's going to be data that don't show up. So in essence, you would compute something, you would compute a speculative result, you would populate your dashboard, but then once a day you would go back maybe six hours later, maybe eight hours later or maybe two days later and you'd go back and recompute everything. And that is "your golden source of data" and you would save that. So you would build two pipelines, one pipeline that has low latency but not very accurate, very speculative and a second pipeline that is very high latency so the data don't, aren't available until three days later but it is accurate and you also balanced throughput. You balance fault tolerance in building these two data pipelines. So that was a state of art. You would do it twice. Sometimes you would do it many times, right. You may do your batch processing after six hours, after 12 hours, after 24 hours, after a week etc. but as you can see that's pretty wasteful. The reason that you would do this was because if you have streaming data, continuously arriving data, they're going to arrive out of order. So all of these points that are circled are all data values that are produced at eight o'clock. Some data comes almost immediately at eight o'clock. Some other data comes in at 8:30 and but there is some piece of data that comes in a full seven hours later. Then if you're going to be computing an aggregate what is the total of all of the things that are produced at eight o'clock? Any result that you compute at nine o'clock is not going to be correct because you're going to have some data that's coming in so late. And because of this, because continuously arriving data can come out of order, can be delayed because latency is a fact of life, that's why people build two pipelines.
## We could split it into processing time windows...
Now, you could take your processing and split them into processing time windows. You could say, I'm going to compute at 9 o'clock all the data received between 8 and 9. I'm going to compute at 10 o'clock all the data received between 9 and 10, and so on. But of course if you do that, if any result you compute at 10 o'clock is not going to contain this late event that arrived at 1 PM. And so the results you computed at 10 o'clock is going to be incorrect. If you use fixed time windows, you're going to lose information about related events, and not just about related events that arrive later. If you recall, in our diagram, we had some data that arrived almost immediately. So if, for example, we split our dataset Into streams, and after we split our dataset into streams, we compute at 10 ten o'clock all the data arrived that arrived between 9 and 10. What happens to data that were produced at 8 o'clock, that happened to arrive at 8:45? It's going to be part of the 8 o'clock to 9 o'clock fixed time window. It's not going to be included in the 9 to 10 window, and that's wrong, because now any sum that you say at 8 o'clock, the total number of trades was 300,000 and odd. And now any trade information that you got, that actually arrived less than an hour late, is a problem, or less then a minute late. Right, how, you can treat this as hours, or minutes, or seconds, or however you want to think about it. Depending on the problem that you're thinking about, the concept is the same.
## A programming model for both batch AND stream
So what we want is that we want to have one pipeline that allows you to carry out these trade-offs, get accuracy, deal with low latency, deal with high latency. What we want is a unified model for processing batch and stream and something that can run On both batch and stream data. And that's what Apache Beam gives us. So Apache Beam is an open source product. It was based on several internal products at Google, Flume and etc.. But it's been open source. It's managed by the Apache Foundation, and it can run on multiple runtimes, not just on cloud Data flow. Cloud Data flow is an execution framework that runs Apache Beam pipelines. But there are others as well. There's runtimes for Spark, there's runtime for Flink and so on. But what we will talk about is a runtime that is Dataflow.

So with Apache Beam, what you have, the key concept is a concept called a window. And the idea behind a window is that a window is a time-based shuffle. So in other words, you will basically compute a window. So here's your input data that's streaming in. It's unbounded. And then you say I want a window of seven to eight. So let's say it's from the seventh minute to the eighth minute or the eighth minute to the ninth minute. And this window is going to contain the data
with a time stamp that is between 8:00 and 9:00. So the key difference here is that it's not that we received it between 8:00 and 9:00, but that the time stamp of the message, it was published between 8:00 and 9:00. So all of the data that we publish between 8:00 and 9:00 Is going to get shuffled into this window wherever it occurs in the input data string. So we are basically making a distinction between the event time and the processing time, okay? The time at which the event occurred and the time with which we get to process this event. And the processing time, the time with which we get to process the event, could be later because of latency. But we will know the event time, and we will basically window all of our events based on that event time. And then we'll carry out the processing. So Beam supports this idea of a time-based shuffle where you are essentially taking events out of this input stream, and you're putting them into the right window to calculate the results.