# Notes on Convolutional Neural Nets for Sentences
This file will have some notes that I create while learning and reading up for a Deep Learning module presentation at the University of Bristol

It's obtained from a lot of different sources, which means it's not directly my own material but instead a word dump of related reading and topics, just "summed up".

This _should_ function as a quick read for someone looking through a window into NLP and NNs, but I assume knowledge of the key "buzzwords" of NNs, but will define NLP stuff as I go along.

By the end of the document, hopefully everyone reading (and me writing) would've reached the end understanding how a Convolutional Neural Net is beneficial for NLP.


## N-Grams and Bag of Words

N-Gram is a contiguous sequence of n words:
- " Dog that barks does not bite"
- Unigram: Dog, that, barks, does, not, bite.
- BiGram: Dog that, that barks, barks does, does not,
not bite...

Bag-of-Words: Method of representing the data using n-grams, usually unigrams.
  **It does not keep track of the order of the text**
  

# Representations of Text:

- One-Hot Encoding:
    Each vector corresponds to a unique word in the corpus. The vector is of length of corpus, and marks 1 where the word is present, 0 elsewhere. It's a **good input to functions that requre a paritcular input range, for example neural nets**
- N-Gram Models:
    N Sequence of tokens into account. Ususally part of another implementation, not much use alone.
- Word2Vec:
    Algorithm that comprises of 2 models, **continous bag of words** or **skip-gram**. The embeddings are created via a **two-layer neural network**, where the model corresponds to a prediction task.
    
    For **CBOW**: Objective is predict a word given some context (autocomplete). 
    For **Skip-Gram**: Predict the context of the given word. 

    The final output of the model is used only for training reaons, because we ignore the output vector and only look at the weights in the hidden layer, which is used as the vector representation for the texts.
    
    
### Explanation for CBOW and Skip-Gram

- Classic Word2Vec: 
It uses a single hidden layer, fully connected neural network. 
The neurons in the hidden layer are all linerar neurons, and the input layer is as big as the vocabulary of the training set.
The hidden layer is set to the dimensionality of the resulting word vectors. The output size is same as the input size.
The input to the hidden layer can be represented by size V x N (v = num of words, N dimension of word vectors). The output of the hidden layer is represented by NxV. 
Where N is the number of neurons in the hidden layer (it matches the dimensionality of the word vector needed). 
Word2Vec creates a vector by **converting the activation values of the ouput layer neurons to probabilities using the softmax function**. 


- Continous Bag of Words Model:
Similar to the Word2Vec, but we'd feed in multiple words, (n) as the input, and the output would be the associated expected word. This way we can train the net to expect n inputs and provide a valid output that's the expected word. 
n words represent context, and the output is known as the target word.

- Skip-Gram Model: 
The model reverse the use of target and context word. The target word is fed as the input, and the context is the expected output. 
It would produce n vectors for each input word, which means you'd expect multiple layers of output, that are sized N(dimension of vector). 

# Word2Vec Explained in more depth:
[I use this as the main resource for this section tbh](https://www.knime.com/blog/word-embedding-word2vec-explained)

word2vec is based on a classical feed-forward, fully conencted architecture.

Given a pairing of "(context, target word)", we can have two different methods of approaching this.
1. **CBOW**: Continous Bag of Words which says GIVEN a context, PREDICT the target word 
2. **Skip-Gram**: GIVEN a target word, PREDICT the context it came from


For a context size of **c = 1**:

If a fully connected NN is used, with one hidden layer, we end up with an architecture where the input and output patterns are one-hot encoded vectors. With a dimensionality of 1xV where V is the vocabulary size.


In the CBOW strategy, the one-hot context word feeds in the input and the output word is a one-hot vector of the target word.

In the case of skip-gram, the one hot encoded missing word feeds the input, where the output tries to produce the one hot encoded context.

To ensure a probability based value, **we use a softmax function in the output layer**

The hidden layer output's actually have a smaller dimensionality in comparison to the output layer, and therefore **the hidden neuron outputs provide the word embedding, this word representation being much more compact than one-hot produces a much less sparse representation**.

For a context size of **c= 3**:
We need to change the network structure. For CBOW approach, we need C input layers of V size to collect C one-hot encoded word vectors, and the hidden layer then provides C word embeddings, each of N size.




# GloVe: Word Embedding

### word2vec problems
Let's start by summarizing word2vec: This trains word embeddings by optimising a loss funciton with gradient descent (classic NN), the loss function is computed **by measuring how well a certain word can predict it's surrounding words**. word2vec **only takes local contexts into account!**. We need to take global count statistics into consideration in sentences.

word2Vec has/had the advantage over prexisting vector/matrix representations due to the simple vector arithmetic that could be done over the values of the vectors created.
For example

> king - man + woman = queen

Was valid maths in the values generated by the word2vec system. (**Called Dimensions of Meaning**).

**GloVe wanted to take both the dimensions of meaning and the global information into consideration**


## GloVe "Step By Step"

First step of GloVe is to remove the need to stream the sentences, by creating a co-occurance matrix. Specifically using a fixed window size, looking at the number of times a number is seen. This leads to a symmetric matrix as the presence of A indicates B and this applies vice versa as well.

Now we look at how these statistics of co-occurance affect the words themselves. We understand the principle that **co-occurance ratios between 2 words in a context are strongly connected to each other**
Example given in [the paper blog i'm using to understand this](http://mlexplained.com/2018/04/29/paper-dissected-glove-global-vectors-for-word-representation-explained/) is that you can expect "ice" and "steam" to be related, and "ice" to be more often seen with "solid" than "gas"and neither to be seen related very much to "fashion".


Co-Occurances are great in understanding meaning, but phrases like "it is" would dominate the loss function, which means we don't want the representation to be too heavily based on just frequency. This is handled using a fancy equation mentioned in the link above.



# Text Classification Pipeline:
[Some Ref](https://medium.com/jatana/report-on-text-classification-using-cnn-rnn-han-f0e887214d5f)

1. Training Text: Input text which is used for learning to predict the classes
2. Feature Vector: A vector that has information describing the characteristics of the input data
3. Labels: Categories or lables our model should predict
4. ML Algorithm: The algorithm (maths) being used to learn over the data, in our case it's CNN.
5. Predictive model: The model we create taht can perform label predictions


# Yet Another CNN Explanatation for NLP

[This is based on a different resource though](http://www.wildml.com/2015/11/understanding-convolutional-neural-networks-for-nlp/)

**Obvious note**: The weights talked about in an NN are actually the matrices applied as the convolution (the filter's values = weights = thing being changed)

Two aspects of the computation in a multi layer convolutional neural network are:
1. **Local Invariance**: Doesn't matter where an object is in an image, just that it's present.
2. **Compositionality**: each big object is _composed_ of smaller objects, i.e a structure will be composed of smaller shapes from edges and pixels.

## Text Embeddings:
The text is embedded using GloVe or word2vec, or even one-hot vectors that index the word in the vocabulary. These are represented using an n dimensional embedding, so k words would have a KxN sized matrix as the input into a NN.

This is our "image".

## Filters:
In images, the filters will slide over local patches ofan image, however in NLP they slide over full rows of the matrix. This means that the width of the filters is the same as the word embedding (n).

The height of the filters (or region size) which represents the number of words together would vary, but the width will always be the same.

## Why CNN's

So, looking at the local invariance and the compositionality aspects of image processing / analysis using CNNs one could question why do we need these properties for words?
You'd imagine that we _really_ care where a word shows up in a sentence, and how it affects the words around it. 

Thing is CNN's are VERY fast for analysis, and can create a much better representation for a large number of words, than something like a BoW or an n-gram system could. CNN's are built to run on GPUs and work super fast as well.

Convolutional filters learn good representations for low level and high level without needing to represent the whole vocabulary to the system.


## Convolution NN's in Depth (Hyperparameters)

### Narrow vs Wide convolution:
Applying filters inside the matrix is okay, but how do we apply them to the edge cases?

We can use zero-padding on the elements, and get a larger or equally sized output into the next layer. 
**Adding zero-padding is called Wide Convolution**

If you didn't use zero padding, **you would get narrow convolutions**

### Stride Sizes:
The standard stride size is 1, however using a larger stride would give you a similar effect to that of an RNN, via the fact that it creates a tree system.

### Pooling Layers:
Pooling layers are a good way to force values to have some relation. They bring down the dimensionality of the input, and force the NN to find relations at higher levels in the values. This enforces a fixed size output matrix which is often required for classification.


In NLP **often we just apply max pooling to the whole filter** meaning that if we have 1000 filters, we can have a 1000 dimensional output.

Pooling also reduces the output dimensionality, but usually keeps the most salient information. An example to think of is that a filter may contain the notion that there is a negation such as "not amazing" in a sentence, and if this phrase occurs in a part of the sentence, we will get a HIGH value there, and a low value (from this filter) everywehre else. 

By performing the max operation, you're keeping whether or not this phrase was present in the sentence by pushing down the smaller values, but as a side effect we lose the information about where exactly it appeared.

But we keep the local information (order of "amazing not" vs "not amazing") in the neural net which is quite important to the understanding of the system.

> "not amazing" would be high valued by this filter, however "amazing not" _should_ be lower valued as the order matters, whereas "amazing not bad", if selected "not bad" would still give us a high value.

### Channels:
Channels in images are things like RGB values, which can provide various layers of informtion for the neural net to consider. In the cases of NLP, we can use these channels to represent different embeddings (word2vec & GloVe), or even have the same sentence represented in different languages or phrased in different ways.


# Applications of NLP Convo Neural Nets

The natural fit for CNNs is classification tasks such as Sentiment Analysis, Spam Detection and Topic Categoriszation. 

This is because the pooling operations may lose the global location, however we don't always care about these details. However, if the need exists, it's possible to fit it into the architecture.

## Sentiment Analysis and Topic Categorization Task:

1. Input layer is a sentence of concatenated word2vec embeddings.
2. Followed by convolution layers with multiple filters
3. Followed by a max-pooling layer
4. With a Softmax classifier.

The input also uses multiple layers, such as using a static and dynamic word embedding, where one is changed as the network runs and one remains the same.



