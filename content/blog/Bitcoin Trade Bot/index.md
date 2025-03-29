+++
date = 2021-12-15
title = "Transformer Bitcoin Trading Bot"
description = "Transformer Network to trade Bitcoin."
authors = ["Alyn Musselman"]
[taxonomies]
tags = ["Python", "Pytorch", "Machine Learning"]
[extra]
math = true
image = "transformer.png"
+++


## Motivation

I chose to make this project because I needed to get more familiar with ML. I had read all about CNN's, Auto Encoders, Variational Auto Encoders, LSTM's, RNN's and even built a couple of them, but I wanted to make a stand alone ML project. I chose the transformer architecture for 2 reasons. Firstly, it was the most complex architecture out at the time. Secondly, it seemed to handle time-series data fairly well and with lower training cost than some of the other models.

## Methodology

To do this project I needed to:
	- Build the model from scratch
	- Collect + Cleanup old Bitcoin data
	- Tokenize the data
	- Batchify the training data
	- Train the model
	- Test the model
	- Let it loose

Building the model from scratch took a long time, even with the many tutorials I used:
https://nlp.seas.harvard.edu/2018/04/03/attention.html
https://jalammar.github.io/illustrated-transformer/
https://rscircus.github.io/posts/2020-02-22-transformer/

Luckily the article "The Annotated Transformer" was so detailed that once I had built the model outlined there I only needed to do the data-handling in order to train the model.

The dataset I used was taken from this Kaggle page:
https://www.kaggle.com/datasets/mczielinski/bitcoin-historical-data

There were quite a few NaN and 0 values in the dataset that had to be removed, but lucky for me there ended up being just enough data remaining to train the model.




## Results