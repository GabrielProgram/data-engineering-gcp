## Streaming => data processing for unbounded data sets

So what does streaming mean in this context?

Essentially, we're talking about doing data processing, not on bounded data but on unbounded data.

Unbounded is a key here.

And typical data sets are bounded. What this means is that they're complete. At the very least, you will process the data as if it were complete. Realistically, we know that there'll always be new data but as far as data processing is concerned, we'll treat it as if it were a complete data set.

Another way to think about bounded data processing is that we'll be done analyzing the data before new data comes in.

On the other hand, if you have an unbounded data set, it's never complete and there's always new data that's coming in. And typically the data's coming in even as you're analyzing the data. So we tend to think of analysis on unbounded data sets as this temporary thing, carried out many times,

being valid only at a particular point in time.

So streaming is essentially data processing on unbounded data. Bounded data is data at rest. Stream processing is how you deal with data that's not at rest, with unbounded data.

But more broadly, people often talk about streaming as an execution engine. The system, the service, the runner, the thing that you're using to process unbounded data.

And if you design it correctly, such a stream processing engine can give you low latency, can give you partial results, speculative results, results that you can change later. The ability to reason about time, to control for correctness of the data, and the power to perform lots of complex analysis, even as the data are coming in. So that's what we're going to learn in this course. We're going to learn about designing stream processing systems

that can do all of these things.

## Unbounded datasets are quite common
That's all. That's all well and good. But how common are unbounded datasets. Why should you care? Well unbounded datasets are actually quite common and becoming more and more common as centers get cheaper and network connectivity keeps improving. For example traffic events, so you may have sensors laid out around thousands of highway miles, thousands of miles of highway and in this course actually, we'll use traffic data from San Diego which is a city in southern California. Data that were collected over a year. So in some senses data that's bounded, it was collected, its historical data. But we're going to simulate it as if it were streaming in real time. Or take for example usage information of some cloud component by every user within a Google Cloud Platform project. For monitoring purposes this will be streaming data. This is a very common theme by the way. You want to derive insights fast and in order to derive insights fast you're going to be monitoring data as it's coming in. Our take for example, credit card transactions, if you're considering the credit card transactions of every card member ever since they opened the account and as they are using the credit cards then you're talking about streaming data. And this is necessary for things like fraud detection. So this illustrates two other common themes. One, the need for fast decisions. This idea that you have to stop the fraud before the person actually makes the purchase. That's the reason you need to process the data as it's coming in. It's not enough to collect the credit card data and process at once a day for example. You need to process that as it's coming in. And that need for a fast decision is what leads to streaming. The second common theme here is that you have lots of data from a variety of sources and it keeps growing over time. But not just that, it's not only about the new data. Think about a fraud situation. It's not just about a purchase that's being made right now. It's also about the history of purchases that this user has made. So you're not just talking about new data, you're talking about comparing the new data with all the data that you've collected so far. Or take the final example, user moves in every user in a multi-user game. Right. An online game that's a streaming dataset. This is a sort of use case that probably didn't exist a few years ago. A few years ago, games were static but now Games are highly personalized and then when you think about things like character design in-app purchases these are all hugely dependent on knowing up to the minute user activity and when you're thinking about up to the minute user activity you are thinking about an unbounded dataset, you're thinking about streaming.

## The need for fast decisions leads to streaming
So to summarize, the three things that we saw in the four examples on the previous slide. Number one, massive data from a variety of sources that keeps growing over time. Secondly, they need to derive insights immediately so that you can display them in the form of dashboards. For example in the case of the cloud monitoring situation. And finally, the need to make decisions fast to interact with users at the right time to make timely decisions. So the demand for stream processing is increasing a lot these days. It's not enough to process large data. You have to process it fast so that a firm can react to changing business conditions in real-time. Real world stream processing use cases include things like trading, fraud detection, system monitoring, order routing, transaction cost analysis, the list goes on and on. Say if you want to look for fraud transactions historically well that batch. But if you want to look at it as that happens that's streaming. So you need stream processing to answer questions like how many sales did I make in the last hour due to advertising conversion? Well that's the last hour. It's new data, it's streaming. It's monitoring. Which version of my webpage do people like better? You want to show this is a dashboard to your user interface designers, than it streaming. You want to do this after the fact you run an experiment and then you want to come back a week later and analyse it. Well that's batch. So streaming is about the need for making faster decisions. Or for example which transactions look fraudulent? You want to stop this scammer before the purchase goes through. So that streaming as we get.

## The Three Vs of Big Data
So when we talk about big data, when if you have terabytes and petabytes of data, we talked about this in our course when we looked at severless data analysis. The way you solve for large amounts of data is to do MapReduce, is to break up your data, do auto-scaling analysis of this data. So we looked at BigQuery, we looked at Dataflow, but we looked at them as batch processing solutions over large amounts of data. The cool thing is that on Google Cloud Platform, both these products - both BigQuery and Dataflow - make an entrance in streaming also. On GCP, the same tools you use for batch can also be used for streaming. Another aspect of big data is variety: audio, video, images, etc. Unstructured text, blog posts. The hard problem with variety is dealing with unstructured data. If your data are all structured, you would just throw it into a relational table and use joints. But unstructured data is a little harder. In the courses on unstructured data, we talked about machine-learning APIs to make sense of variety. But the third aspect to big data is near real-time data processing; data that's coming in so fast that you need to process it to keep up with the data. That's what we're going to be talking about in this course when we talk about streaming. So if you think about a big data architecture, you will have several parts to it. Often, masses of structured and semi-structured historical data that you may have stored in cloud storage or POP server, or BigQuery. That's volume and variety. On the other side, stream processing is what is used for fast data requirements for velocity. Both complement each other. So if you think about Internet of Things, for example, you're talking about increased data volume, probably increased variety and velocity of data. And so this leads to a dramatic increase and need for things like stream processing technologies.

## Stream data processing: Challenges
So let's now move on to talk about the challenges associated with stream data processing. So stream processing is what makes it possible to derive real-time insights from growing amounts of data. So if you're thinking about a good stream processing solution there are three key challenges that it needs to address. Number one, it needs to be able to scale. Think of credit cards, there'll be hundreds of thousands of transactions happening all the time and the volume is not going to be consistent. It's not going to be exactly the same at all times. Around holidays, the volume is going to be higher. Late at night the volume is going to be lower. Second challenge, you want to essentially use continuous queries. Lots of times we're interested in querrying the latest arriving data. For example, we might want to do things like moving averages looking for unusual spikes but in order to do that, we have to continuously calculate some kind of mathematical statistical analysis. We have to do this on the fly on the stream but at the same time we have to account for things like late data, out of order data etc. Third challenge. We can't just stop the insight. I mean we can just stop the ingest whenever we want to analyze the data. We need to be able to derive the insights even as the data are coming in. In other words, we want to be able to do SQL-like queries that operate over time windows, over time windows, over that data. So when you think what stream processing systems, they've been around awhile. They were born nearly a decade ago because of the need for low latency processing of large volumes of dynamic time continuous streams. Our sensors and monitoring devices etc. So they've been around and probably the first users were in the financial industry with stock market data. But today you see it everywhere. You're generating stream data through human activities, through social media, through machine data, through sensor data. So there's a lot of stream data out there. So we need to know how to process it. 