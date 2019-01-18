0:00
But, once you have done this, though, you no longer have an independent dataset. And if you want to evaluate the final model, you want to say, okay, here's the final model that I have, how well does it perform? Well, you've kind of used all this data, this validation data, in order to choose the model parameters. So, you cannot use the performance on the validation data as a measure of how well your model works. What you will have to do is to go get some more data. Some completely independent data, and one way to do this is to wait for the passage of time. Maybe you're creating a model and you're training it on historical data from 2016, and then once you have 2017 data, you use that as your test data set, and then you're ready to go. But, another way to do this would be to take your original data and split it as training data and validation data and change the splits maybe ten times. This is called cross-validation and the average of all of those tells you how good your model is doing. 