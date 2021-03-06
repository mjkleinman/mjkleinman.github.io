---
layout: post
title: AlphaZero Explained (for chess players)
---

The last two weeks were pretty exciting for chess. 
Google DeepMind published a [paper](https://arxiv.org/abs/1712.01815) detailing how they created a chess engine, AlphaZero, that was able to crush the top computer program, Stockfish, beginning only with knowledge of the rules of the game. 
The games were pretty remarkable.
Unfortunately, to many interested chess players, the details behind the algorithms — the methods AlphaZero was employing to make their moves — remains a complete mystery. 
I’m hoping this post will give an overview of how AlphaZero was coming up its moves. 

**How is AlphaZero coming up with its moves?**

Imagine we had a way to estimate the probabilities for "an expert” to make each available move in a chess position, without searching at depth. 
That is, just by looking at the position, could there be some intrinsic patterns, that in itself might suggest the moves that should be played? 
For instance, if one player has a passed pawn, we might expect that advancing this pawn might be given a high probability, given the general rule that “passed pawns must be pushed".
Of course, these “instincts” into a position need to be investigated, and this is done by evaluating the possible resulting positions. 
This is what has allowed tradional chess engines to play the game so well. They can search through millions of positions per second. 
If engines could somehow focus their search on promising variations, they could potentially play even stronger. 
This is what AlphaZero does and what separates it from traditional chess engines.

Lets suppose we were designing a chess engine.
If we had a way to get a reasonable characterization of potential strong moves in the position without going into depth in the position, we might be able to use this “intuition” to guide our search. 
So for now, let’s suppose that we have a way to do this — that given any position, we can reasonably quantify the probabilities of an expert making each possible move. 
To get a more accurate assessment of the position — to validate our intuition — we need figure out the evaluations of the subsequent positions that might arise.
A reasonable way to search through the possible positions might be to focus our search on the moves with the highest probability of occurring. 
We could continue the game, evaluating each subsequent position with our “intuition function” and continue choosing the move with the highest probability of occurring, until we reach the end of the game.
In subsequent iterations of the search we could incorporate the knowledge of whether the position(s) we searched resulted in a positive result, as well as explore new positions (because our intuition function does not need to be perfect and need not find the best move initially). 
This is how earlier versions of AlphaZero (applied to Go) worked. 

In this version of AlphaZero, an approximate position evaluation is employed as a proxy for the result of the game, allowing the search to terminate early and instead focus on positions whose evaluations are more relevant to the root game position. 
As long as our "intuition function” is reasonable — as long as we can reasonably characterize the available moves in a position, the information gained when evaluating the positions in our search should be informative to the initial root position.
The search itself basically refines our initial intuition, performing a sort of averaging of the resulting positions. 
We can think of the search as a function that takes in as inputs the initial move probabilities, and spits out a more accurate set of final move probabilities.

We relied heavily of having what I called an “intuition function”, a way of evaluating a given position, without going into a position’s depth. 
AlphaZero achieves this through what is called a neural network, which sounds complicated, but is basically a collection of variable weights that can be tuned to achieve a particular function.

**What is a neural network?**

A neural network is a nonlinear function that simply put, transforms inputs to outputs. 
In our case, we need to somehow transform the input — that is a given position — into a set of move probabilities (and also an expected value output). 
A neural network sounds mysterious, but is actually made up of very understandable components. 
It consists of layers, with each layer performing a different operation, as well as connections between the layers. 
These connections are often called “weights”, and correspond to how important the previous layers are (i.e how much the values from the previous layers need to be weighted).
The exact architecture of the network, for instance the number of layers and their type, is a bit of a mystery, even for researchers who study neural networks extensively. 
It still is an empirical science, and certain architectures are found to work better for certain tasks.

It turns out that given sufficiently large training data, we can usually tune these weights in such a way that they can produce the desired outputs.
In our case, our output consists of the initial move probabilities, outputted by our “intuition function”, or neural network, as well as a term that corresponds to the expected value of the game, given the current position.
After a game of self play, we have the final result of the game, and can therefore minimize the difference between the actual and expected outcomes. 
We can tune the weights such that they should have produced the actual outputs.
To improve the initial move probabilities outputted by the neural network, we would need a characterization of improved move probability distributions, so that our weights could be tuned to reflect the improved probability distribution. 
Our improved move probability distribution takes the form of the probability distribution after our search, which we have for each move! 
So, we can have a cost function that includes both a term that minimizes the difference between actual and expected outcome, as well as a term that minimizes the difference between initial and final move probability distributions. 

To be able to tune our weights to train our neural network or "intuition function", we need lots of data. 
In the case of this paper, they create their own data, by pitting the current version of neural network parameters against previous versions. 
By playing millions of training games, and by tuning the weights to match the more accurate move probability distributions, the neural network is effectively trained to look for important patterns, characteristics, or features of a position. 
Then, at the end, just by looking at a position, it is able to have a reasonable characterization of the most likely moves in the position, which it can then use to guide its search.

**Concluding thoughts**

Some of the games that AlphaZero played were truly special — its ability to weight piece activity, and play long-term positional material sacrifices was quite remarkable. 
It was the first time, for many chess players, that we were blown away by computer chess. 

I’d be really curious to know how similar, after training, the initial move probabilities are from the final move probability distributions. 
Moreover, based on the potential similarity, or lack thereof, can we perhaps characterize certain positions, in the sense that less concrete positions, those of a more “positional nature” might have initial move probability distributions more similar to final move probabilitiy distributions, whereas tactical positions perhaps consist of larger differences between the initial and final move probability distributions? 

