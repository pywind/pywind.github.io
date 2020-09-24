---
layout: post
title:  "K Nearest Neighbors"
date:   2020-09-24
excerpt: "One of the simplest algorithms in supervised learning."
tag:
- supervised
- ML
comments: false
---

## General

The K-nearest neighbors (KNN) algorithm is a simple, easy-to-implement supervised machine learning algorithm that can be used to solve both classification and regression problems.

The KNN algorithm assumes that similar things exist close to each other. In other words, similar things are close together.

<figure>
	<a href="https://3qeqpr26caki16dnhd19sv6by6v-wpengine.netdna-ssl.com/wp-content/uploads/2014/09/k-Nearest-Neighbors-algorithm.png"><img src="https://3qeqpr26caki16dnhd19sv6by6v-wpengine.netdna-ssl.com/wp-content/uploads/2014/09/k-Nearest-Neighbors-algorithm.png"></a>
	<figcaption> This graph is a set of groups with common properties
    </figcaption>
</figure>

The entire training dataset is stored. When a prediction is required, the k-most similar records to a new record from the training dataset are then located. From these neighbors, a summarized prediction is made.

There is no model to speak of other than holding the entire training dataset. Because no work is done until a prediction is required, KNN is often referred to as a lazy learning method.


