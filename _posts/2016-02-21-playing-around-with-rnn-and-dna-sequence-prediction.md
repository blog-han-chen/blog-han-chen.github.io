---
layout: post
title: "Playing Around with RNN and DNA Sequence Prediction"
description: ""
category: machine-learning
tags: [maching-learning bioinformatics]
---
{% include JB/setup %}
{:center: style="text-align: center"}

###Introduction
RNN stands for recurrent neural network and is a very popular model for squential data processing, such as natural language modelling. After seeing the success of RNN at text prediction in this [post](http://karpathy.github.io/2015/05/21/rnn-effectiveness/). I had an idea of using it to predict DNA sequence and see what kind of interesting patterns it can pick up. The result was not amazing, in fact, not at all, but do read on if you are interested in the process and comment below if you have suggestions. 


###Background 

A cDNA refers to coding DNA, it is a sequence of 'A', 'T', 'C', 'G' base pairs. 3 of the consecutive base pairs are a codon and encode for amino acid--building block for protein. cDNA always starts with a start codon 'ATG', and ends with a stop codon, either 'TAA','TAG', or 'TGA'. 

Coursera offers great introduction to neural network: [Andrew Ng's Machine Learning](https://www.coursera.org/learn/machine-learning) at week 4,  and recurrent neural network: [Geoffery Hinton's Neural Networks](https://www.coursera.org/course/neuralnets). I also read this [post](http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-1-introduction-to-rnns/) after I have a basic understanding of neural net. 


###Modelling with Vanilla RNN: 

The basic idea of sequence prediction is that given all previous words in a sentence, predict the next word. In our context it might look like this: 

<div style="text-align:center">
    Given: SENTENCE_START A T  
    Predict: G
    A cDNA sequence should always start with start codon, ATG
    Given: SENTENCE_START A T G T C C G C A T G A
    Predict: SENTENCE_END
    A cDNA sequence always ends with a stop codon, in this case, TGA
</div>

Given a complete cDNA seq `A T G T C C G C A T G A`, our training example looks like: 
```
X (input): SENTENCE_START A T G T C C G C A T G A
Y (output): A T G T C C G C A T G A SENTECE_END
```
We would punish the neural network if, given `SETENCE_START`, it doesn't predict `A`, and given `SENTENCE_START A`, it doesn't predict `T`. Batching them together, given sequence X, the network should predict a sequence similar to Y; it is punished for each character predicted wrong. 

As a start, I am using E. coli cDNA data set downloaded here //TODO. The file is about 5Mb big and contains 4000+ cDNA sequences. For real neural network training it is small, but it should suffice as a toy dataset. 

It is easy to parse the sequences using BioPython
```
from Bio import SeqIO
records = SeqIO.parse("data/e_coli_cdna.fa","fasta")
cDNAs = [list(r.seq) for r in records]
print cDNAs[0]
# ['A', 'T', 'G', 'A', 'A', 'A', ... ]
# adding sentence start and end token
cDNAs = [["SENTENCE_START"] + sent + ["SENTENCE_END"] for sent in cDNAs]
```

Since DNA has really simple vocabulary, we can turn sequences of characters to and from sequences of indices

```
index_to_word = ["SENTENCE_START",'A','T','C','G','N',"SENTENCE_END"] # N for unknown base pair
word_to_index = dict((c,i) for i,c in enumerate(index_to_word))
encoded_sentence = [word_to_index[c] for c in cDNAs[0]] #encode the first cDNA
print encoded_sentence
# [0, 1, 2, 4, 1, 1, 1 ... ]
```

The indices are further processed to one-hot vector (vector with all 0's plus one 1) and fed in our RNN. I won't go into the details here but you can read this [tutorial](http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-2-implementing-a-language-model-rnn-with-python-numpy-and-theano/) if you are interested. 

###Result

After training on an I7 CPU. I have the learning curve plotted, with about 3900 training examples and 400 testing examples. 

{% include image.html
            img="image/rnn-dna/naive-rnn-learning-curve.png"
            title="Naive RNN Learning Curve"
            caption="The training loss and testing loss are almost the same. Further the loss does not decrease after the 3rd epoch" 
            display="inline" 
            size="medium" %}

Where one epoch here means the neural network iterated the entire training set, 3900 examples, using stochastic gradient descent. 

Note that the training loss, or training error, is similar to testing loss. If you remember from Andrew Ng's machine learning course, this is exactly the bad sign of underfitting! 

To ask the network to generate a few sentences: 

```
NNATAAGTGAACCCTGTTGAATAATGATCTGGTTAATAACTGAGAATTAACGATAACTAAATAA

NSENTENCE_STARTNCNCCTTCTTATGAGTTATTAATAGTAACTTAATAAGTAAGTTTCAGTAATGAATAAATAATGTATTAGTACTGAATAATGACTAATSENTENCE_STARTAATAACTAATAACTAGTAAATAACTGAAACCTAAAATAATTATAATAGTTAATGAGTAATAAACTAATCTAATTAAAAGATGAAAGGTAGTAGGATAAATTGAATGGGTGTAGNAATAAGTAAATGTGCCTTAAAGTATGATTAAGATAATAATAAAATGTAAGTAAGTGAAGNTGAATAATAGCTGTGAAATAATGTAAATTGAGTTAATAAGCTAAGTGATTAACACTGAAATCTAATAATAATAAGTATTAAGTAATAAATAACTAATGTTAAATAAATGAGTAATAAGTGATGAAGTAAGCTGTAGTAGCTAATAAATTTATTAATGGAATTTAAAACTGAAGTAACTAATTTGAATTATCTGTAAATAAAGTATCGTTTAATAATAAATCCTTAATTAACTAAATATTTCTTAAGTGAATGAATTAATAACTAAGTGTAAGTAGCCGTGATTTAAATGAAATATGATAGTAAATGTGTAAATAATAACTGATTTCTTAATAAGCAGGGATTTCTGAGCTAGTAAAATTAAGTAATAGTGATANGCTTAATANATAAATGACATAATTAAACTATATGAGTGATGAAGGCTATAAATAAGTAATTTTAATTTAGATCCTTTTAATTAAATGAATTAAGTAATAAATAAGTAATATGAATAAATTAATTAAATGATTAAAATAACTAAANATGAATTAAAAATGAATAGTAAATAATCATAACTTGCTTAAGTTAAATGTTAAGTAGCTGAATAATGGTAANAGTAATAAAGATCTAACTAATCNCTAATTTAATAACTAATAATTNAATAAATTTAATGAAATGAACTAACCTAAATGATAACTNATATGGATAATGCATAATGATAAGTAATATAATGSENTENCE_STARTTAAAGTTCGATTAATGAATGAGTAAATATTAAATAGTGATGATAATGATGGCTGATAATGATTAATAAAGCTATAATAATAAGCTTTAATA
```

Not very good... It should not mix `SENTENCE_START` token inside a sentence. 

Since the learning curve suggests that the model is underfitting, adding more data won't help. Perhaps we can try something else. 


###Training with GRU

The difference between vanilla RNN and Gated Recurrent Unit (GRU) is that the latter is more powerful. It introduces a gating mechanism for learning long term dependencies between words, similar to Long Short Term Memory (LSTM) model. I don't have a good intuition for that either and probably won't explain it well, but if you are interested, you can read [here](http://www.wildml.com/2015/10/recurrent-neural-network-tutorial-part-4-implementing-a-grulstm-rnn-with-python-and-theano/). 

I started off by using ~10 genes as training set, ~2 as testing set, trained through 2 layers each with 50 GRU hidden units. The reason to choose small dataset is to hope I can overfit the data. Only then would more training examples help (correct me if I am wrong). Plus GRU are harder to train and will take very long for large dataset. The result is shown here: 

{% include image.html
            img="image/rnn-dna/gru-learning-curve.png"
            title="GRU Learning Curve"
            caption="The training loss and testing loss are almost the same. But this time the loss is generally decreasing" 
            display="inline" 
            size="medium" %}

It seems like we underfitted the data again. What about the sentence generated? 

```
CATCATTGAACTGACTGGTAATCAGCCTGTGGTGATGGATGGAACGATGTACAGCGTGACAGGTCGTATCCCGCTGGTGCTTGAAGGGAACCACGGTTGATGC


GACACCATGGCACCGGGGGATGTGGTAATTGTGAAACGCTTCAAGCAGGGGAAGGAATGCCCGTTACCGAATGTCCGGTCAACTTACTGTGGGTTCCGGCGGGGTCCTCATCATCACCGTGCCGGAAGGTTTTTTTGAGCCCAATGTAAGACGAAATAGCTGGACCTCTGCCCGGAAGTGCGGTCTGCAGGAATGTTGGTGTGTGTCCTTCTTTGCCGGGGTGAGTTCGTATTCAAGTCAGTTCCCCGTCCGGATGAATTCTCCAGCAACTGGCTCCCCCCCGTTCAGCGACCCTGTAAACCCTGCTGACCGAAGTTTGTTAAACGGATTCCGGAGCGTGCCCGTCTGAGGTGACCGGGAAA
```

Well, at least the net learned not to include `sentence_start_token` after sentence started. But still, I would expect it to pick up simple patterns such as start and end codon. That is, a sentence starts with "ATG", ends with one of "TAA, TGA, TAG". 


###Final Attempt, 

As a final atempt, I chose 10 training examples and 2 testing examples and fed into 2 layers of GRU each with 512 units, more than 10 times the number before. I only managed to get the first a few gradient updates, since my computer was too slow. 

{% include image.html
            img="image/rnn-dna/gru-512-units-learning-curve.png"
            title="GRU Learning  with 512 units"
            caption="Same as previous ones, testing loss and training loss are exactly the same" 
            display="inline" 
            size="medium" %}

Sentence Generated: 
```
C A G G C C G T G G T G G G C C G G G T T T A T G A G A A C T C A C C C A G A G
```

###Thoughts and Conclusion

Well I started the experiment with high hope and got somewhat disappointed in the end, RNN is not magic afterall!

But there are some lessons learnt: 

1. Genes are generally long, the shortest ones are a few hundred base pairs, longers ones are on the thousands scale! According to this article, RNN generally have hard time learning such a long dependencies. (It is not learning sentences, but essays and books!)

2. I did not choose a good toy example, although DNA is cool, we lack intuitive understanding of them ourselves, which makes it difficult to interpret the result. It is hard to recognize whether the network has picked up a pattern or it is still learning.

3. Machine Learning is about understanding the data. Neural nets are powerful, but it is not a "one size fits all" model. DNA analysis requires something that incorporates more of our existing knowledge into it. 



 
