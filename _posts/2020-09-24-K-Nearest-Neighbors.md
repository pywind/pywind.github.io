---
layout: post
title:  "K-Nearest Neighbors"
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

KNN captures the idea of similarity (sometimes called distance, proximity, or closeness) with some mathematics we might have learned in our childhood — calculating the distance between points on a graph.

> There are other ways of calculating distance, and one way might be preferable depending on the problem we are solving. The Euclidean distance is a popular and familiar choice. More detail in [here](https://https://pywind.github.io/Math-in-ML/)

There is no model to speak of other than holding the entire training dataset. Because no work is done until a prediction is required, KNN is often referred to as a lazy learning method.


## K-Nearest Neighbors Algorithm

### Step by step

1. Initialize K to your chosen number of neighbors
2. For `example` in `data`:
   
   * Calculate the distance between the query example and the current example from the data.
   * Add the distance and the index of the example to an ordered collection.
3. Sort the ordered collection of distances and indices from smallest to largest by the distances.
4. Pick the first K entries from the sorted collection (Doesn't contain current point).
5. Get the labels of the selected K entries.
6. Result:
   *  If regression, return the mean of the K labels.
   *  If classification, return the mode of the K labels.

### Source code with Python

### Data preprocessing

> I highly recommend that you preprocess the data. This makes calculating the distance easy and doesn't have to deal with too large numbers

Make sure you read [this](https://pywind.github.io//advance/) post before reading below.

### Step 1: Get the neighbors

```python
# Locate the most similar neighbors
def get_neighbors(train, point, k):

    distances = list()
    index = np.arange(len(train))

    for row in train:
	# np.linalg.norm: Euclidean distance, default is norm 2
        distances.append(np.linalg.norm(point - row))
	
	# Grab indices and distances together in 1 set
    distances = sorted(zip(distances,index))
	
    # index from 1 because point appears in train
    neighbors = [distances[i][1] for i in range(1, k + 1)]
    return neighbors
```

### Step 2: Predictions

```python
# Depending on the regression or the classification. Here is classification

def prediction_classification(X_train, y_train, k):
    labels = []
    for point in X_train:
        neighbors = get_neighbors(X_train, point, k)

		# Get value of y from the existing index from get_neighbors 
        label_i = [y_train[index] for index in neighbors]

		# from scipy import stats
		# mode of label_i
        most = stats.mode(labels_i)[0]
        labels.append(most)
    return labels
```

### Choosing the right values K

To select the K that’s right for your data, we run the KNN algorithm several times with different values of K and choose the K that reduces the number of errors we encounter.

Keep in mind:

* As we decrease the value of K to 1, our predictions become less stable.
* Inversely, as we increase the value of K, our predictions become more stable due to majority voting / averaging and thus, more likely to make more accurate predictions (up to a **certain point**). Eventually, we begin to witness an increasing number of errors. It is at this point we know we have pushed the value of K too far.
* Usually make K an odd number to have a tiebreaker.

## Advantages

1. The algorithm is simple and easy to implement.
2. There’s no need to build a model, tune several parameters, or make additional assumptions.
3. The algorithm is versatile. It can be used for classification, regression, and search.

## Disadvantages

1. The algorithm gets significantly slower as the number of examples  variables increase.
2. KNN is very sensitive to noise when K is small.

### More source code in [here](https://github.com/pywind/ML_repo/blob/master/K-Nearest-Neighbors.ipynb)

# Thank for reading !!!

## References

1. [Machine Learning Cơ bản](https://machinelearningcoban.com/2017/01/08/knn/)
2. [Toward data science](https://towardsdatascience.com/machine-learning-basics-with-the-k-nearest-neighbors-algorithm-6a6e71d01761)
