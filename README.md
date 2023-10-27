# Work Report

## Information

- Name: <ins> DAS, NEHA </ins>
- GitHub: <ins> NehaDas25 </ins>

## Features

- Not Implemented:
  - PART 0 : Data Sources - Was already implemented.

<br><br>

- Implemented:
  - PART 1: Parts-of-speech Tagging
  - PART 1.1: Training
    - Exercise 1: create_dictionaries
      - Implemented the function create_dictionaries() that takes training_corpus, vocab, verbose=True as inputs.
      - This function returns the three dictionaries that are transition_counts, emission_counts, and tag_counts.
      - emission_counts: maps (tag, word) to the number of times it happened.
      - transition_counts: maps (prev_tag, tag) to the number of times it has appeared.
      - tag_counts: maps (tag) to the number of times it has occured.
      - Initialized the three dictionaries using defaultdict.
      - Initialized "prev_tag" (previous tag) with the start state, denoted by '--s--'.
      - Each item in the training corpus contains a word and its POS tag, so go through each word and its tag in the training corpus and increased the word_tag count.
      - Found the word and tag using the get_word_tag() helper function that is imported from utils_pos.py.
      - Finally get the three dictionaries that are transition_counts, emission_counts, and tag_counts.
      - This passed the unit-test cases.

  - PART 1.2: Testing
    - Exercise 2: predict_pos
      - Implemented the function predict_pos() that takes prep, y, emission_counts, vocab, states as inputs.
      - Returns the accuracy of your model.
      - prep: a preprocessed version of 'y'. A list with the 'word' component of the tuples.
      - y: a corpus composed of a list of tuples where each tuple consists of (word, POS).
      - emission_counts: a dictionary where the keys are (tag,word) tuples and the value is the count.
      - vocab: a dictionary where keys are words in vocabulary and value is an index.
      - states: a sorted list of all possible tags for this assignment.
      - To assign a part of speech to a word,  the most frequent POS for that word in the training set has been assigned.
      - Each time we predict based on the most frequent POS for the given word, we had to check whether the actual POS of that word is the same or not. If so, then the prediction was correct.
      - Calculated the accuracy as the number of correct predictions divided by the total number of words for which we predicted the POS tag.
      - This passed the unit-test cases as well.

  - PART 2: Hidden Markov Models for POS
  - PART 2.1 - Generating Matrices
    - Exercise 3 - create_transition_matrix
      - Implemented the create_transition_matrix() that takes alpha, tag_counts, transition_counts as inputs.
      - Returns the output as a matrix that computes equation 3 for each cell in matrix A.
      - alpha: number used for smoothing
      - tag_counts: a dictionary mapping each tag to its respective count
      - transition_counts: a dictionary where the keys are (prev_tag, tag) and the values are the counts.
      - Here smoothing has been implemented while computing these matrices provided with the formula: 𝑃(𝑡𝑖|𝑡𝑖−1)= 𝐶(𝑡𝑖−1,𝑡𝑖)+𝛼 / 𝐶(𝑡𝑖−1)+𝛼∗𝑁.
      - Each cell gives us the probability to go from one part of speech to another.
      - Finally this gives the output as mattrix A which is a transition matrix. 
      - This passed the unit-test cases as well.

    - Exercise 4: create_emission_matrix
      - Implemented the create_emission_matrix() that takes alpha, tag_counts, emission_counts, vocab as inputs.
      - Returns the B emission probabilities matrix.
      - alpha: tuning parameter used in smoothing 
      - tag_counts: a dictionary mapping each tag to its respective count
      - emission_counts: a dictionary where the keys are (tag, word) and the values are the counts
      - vocab: a dictionary where keys are words in vocabulary and value is an index.
      - Here smoothing has been implemented while computing the emission probability matrices provided with the formula: 𝑃(𝑤𝑖|𝑡𝑖) = 𝐶(𝑡𝑖,𝑤𝑜𝑟𝑑𝑖)+𝛼 / 𝐶(𝑡𝑖)+𝛼∗𝑁.
      - The matrix B is of dimension (num_tags, N), where num_tags is the number of possible parts-of-speech tags.
      - This passed the unit-test cases as well.

  - PART 3: Viterbi Algorithm and Dynamic Programming
  - PART 3.1: Initialization
    - Exercise 5: initialize
      - Implemented the function initialize() that takes states, tag_counts, A, B, corpus, vocab as inputs.
      - This function returns best_probs and best_paths.
      - In this part we will initialize the best_paths and best_probabilities matrices that will be populating in feed_forward.
      - Both matrices will be initialized to zero except for column zero of best_probs.
      - Column zero of best_probs is initialized with the assumption that the first word of the corpus was preceded by a start token ("--s--").This allows us to refer the A matrix for the transition probability.
      - Here are the steps to initialize column 0 of best_probs:
        - The probability of the best path going from the start index to a given POS tag indexed by integer 𝑖 is denoted by best_probs[𝑠𝑖𝑑𝑥,𝑖].
        - This is estimated as the probability that the start tag transitions to the POS denoted by index 𝑖 : 𝐀[𝑠𝑖𝑑𝑥,𝑖] and that the POS tag denoted by 𝑖 emits the first word of the given corpus, which is  𝐁[𝑖,𝑣𝑜𝑐𝑎𝑏[𝑐𝑜𝑟𝑝𝑢𝑠[0]]].
        - Note that vocab[corpus[0]] refers to the first word of the corpus (the word at position 0 of the corpus).
        - vocab is a dictionary that returns the unique integer that refers to that particular word. 
      - best_probs is calculated provided by the formula: best_probs[i,0] = math.log(A[s_idx,i]) + math.log(B[i,vocab[corpus[0]]]).
      - This passed the unit-test cases as well.

  - PART 3.2: Viterbi Forward
    - Exercise 6: viterbi_forward 
      - Implemented the function viterbi_forward() that takes A, B, test_corpus, best_probs, best_paths, vocab, verbose=True as inputs.
      - A, B: The transition and emission matrices respectively
      - test_corpus: a list containing a preprocessed corpus
      - best_probs: an initialized matrix of dimension (num_tags, len(corpus))
      - best_paths: an initialized matrix of dimension (num_tags, len(corpus))
      - vocab: a dictionary where keys are words in vocabulary and value is an index.
      - The formula to compute the probability and path for the 𝑖𝑡ℎ word in the 𝑐𝑜𝑟𝑝𝑢𝑠, the prior word 𝑖−1 in the corpus, current POS tag 𝑗, and previous POS tag 𝑘 is: prob = best_probs[k,i-1]+math.log(A[k,j]) +math.log(B[j,vocab[test_corpus[i]]]).
      - Checked if this path's probability is greater than the best probability up to and before this point, then keep track of the best probability and keep track of the POS tag of the previous wordnthat is part of the best path and save the index (integer) associated with that previous word's POS tag.
      - This passed the unit-test cases as well.

  - PART 3.3: Viterbi Backward
    - Excercise 7: viterbi_backward
      - Implemented the viterbi_backward() that takes best_probs, best_paths, states as inputs.
      - Returns the best path.
      - Got the number of words in the corpus which is also the number of columns in best_probs, best_paths. 
      - Initialized array z, same length as the corpus that is z = [None] * m and get the number of unique POS tags.
      - Initialized pred array, same length as corpus that is pred = [None] * m. Then there are two steps to follow.
      - In Step 1,Looped through all the rows (POS tags) in the last entry of best_probs and find the row (POS tag) with the maximum value. Convert the unique integer ID to a tag (a string representation) using the list states.
      - In Step 2, Starting at the last column of best_paths, use best_probs to find the most likely POS tag for the last word in the corpus.Then use best_paths to find the most likely POS tag for the previous word.And finally update the POS tag for each word in z and in preds.
      - This passed the unit-test cases as well.

  - PART 4: Predicting on a Dataset
    - Exercise 8: compute_accuracy
      - Implemented a function compute_accuracy() that takes pred, y as inputs.
      - Returns the accuracy of the viterbi algorithm's POS tag predictions.
      - Splited the label into the word and the POS tag using y.split().
      - Checked that there is actually a word and a tag no more and no less than 2 items and store the word and tag separately.
      - Checked if the POS tag label matches the prediction then, increase the num_correct.
      - And keep track of the total number of examples (that have valid labels).
      - This passed the unit-test cases as well.
  
      

        
<br><br>

- Partly implemented:
  - w2_unittest.py was not implemented as part of assignment to pass all unit-tests for the graded functions(). It was already created by the Professor.
  - utils_pos.py was not implemented as part of assignment to pass all unit-tests for the graded functions(). It was already created by the Professor.

<br><br>

- Bugs
  - No bugs.

<br><br>


## Reflections

- Assignment is very good. Gives a thorough understanding of the basis of POS,HMM,Viterbi algorithm and Dynamic Programming.


## Output

### output:

<pre>
<br/><br/>
Out[2] - 
  A few items of the training corpus list
  ['In\tIN\n', 'an\tDT\n', 'Oct.\tNNP\n', '19\tCD\n', 'review\tNN\n']

Out[3]-
  A few items of the vocabulary list
  ['!', '#', '$', '%', '&', "'", "''", "'40s", "'60s", "'70s", "'80s", "'86", "'90s", "'N", "'S", "'d", "'em", "'ll", "'m", "'n'", "'re", "'s", "'til", "'ve", '(', ')', ',', '-', '--', '--n--', '--unk--', '--unk_adj--', '--unk_adv--', '--unk_digit--', '--unk_noun--', '--unk_punct--', '--unk_upper--', '--unk_verb--', '.', '...', '0.01', '0.0108', '0.02', '0.03', '0.05', '0.1', '0.10', '0.12', '0.13', '0.15']

  A few items at the end of the vocabulary list
  ['yards', 'yardstick', 'year', 'year-ago', 'year-before', 'year-earlier', 'year-end', 'year-on-year', 'year-round', 'year-to-date', 'year-to-year', 'yearlong', 'yearly', 'years', 'yeast', 'yelled', 'yelling', 'yellow', 'yen', 'yes', 'yesterday', 'yet', 'yield', 'yielded', 'yielding', 'yields', 'you', 'young', 'younger', 'youngest', 'youngsters', 'your', 'yourself', 'youth', 'youthful', 'yuppie', 'yuppies', 'zero', 'zero-coupon', 'zeroing', 'zeros', 'zinc', 'zip', 'zombie', 'zone', 'zones', 'zoning', '{', '}', '']

Out[4] - 
  Vocabulary dictionary, key is the word, value is a unique integer
 :0
 !:1
 #:2
 $:3
 %:4
 &:5
 ':6
 '':7
 '40s:8
 '60s:9
 '70s:10
 '80s:11
 '86:12
 '90s:13
 'N:14
 'S:15
 'd:16
 'em:17
 'll:18
 'm:19
 'n':20

Out[5] -
  A sample of the test corpus
  ['The\tDT\n', 'economy\tNN\n', "'s\tPOS\n", 'temperature\tNN\n', 'will\tMD\n', 'be\tVB\n', 'taken\tVBN\n', 'from\tIN\n', 'several\tJJ\n', 'vantage\tNN\n']

Out[6] - 

The length of the preprocessed test corpus:  34199
This is a sample of the test_corpus: 
['The', 'economy', "'s", 'temperature', 'will', 'be', 'taken', 'from', 'several', '--unk--']

Out[8] -

word count = 50000
word count = 100000
word count = 150000
word count = 200000
word count = 250000
word count = 300000
word count = 350000
word count = 400000
word count = 450000
word count = 500000
word count = 550000
word count = 600000
word count = 650000
word count = 700000
word count = 750000
word count = 800000
word count = 850000
word count = 900000
word count = 950000

Out[9] - 
  Number of POS tags (number of 'states'): 46
  View these POS tags (states)
  ['#', '$', "''", '(', ')', ',', '--s--', '.', ':', 'CC', 'CD', 'DT', 'EX', 'FW', 'IN', 'JJ', 'JJR', 'JJS', 'LS', 'MD', 'NN', 'NNP', 'NNPS', 'NNS', 'PDT', 'POS', 'PRP', 'PRP$', 'RB', 'RBR', 'RBS', 'RP', 'SYM', 'TO', 'UH', 'VB', 'VBD', 'VBG', 'VBN', 'VBP', 'VBZ', 'WDT', 'WP', 'WP$', 'WRB', '``']
   
   Expected Output
    Number of POS tags (number of 'states'46
    View these states
    ['#', '$', "''", '(', ')', ',', '--s--', '.', ':', 'CC', 'CD', 'DT', 'EX', 'FW', 'IN', 'JJ', 'JJR', 'JJS', 'LS', 'MD', 'NN', 'NNP', 'NNPS', 'NNS', 'PDT', 'POS', 'PRP', 'PRP$', 'RB', 'RBR', 'RBS', 'RP', 'SYM', 'TO', 'UH', 'VB', 'VBD', 'VBG', 'VBN', 'VBP', 'VBZ', 'WDT', 'WP', 'WP$', 'WRB', '``']

Out[10] - All tests passed

Out[11] - 

transition examples: 
(('--s--', 'IN'), 5050)
(('IN', 'DT'), 32364)
(('DT', 'NNP'), 9044)

emission examples: 
(('DT', 'any'), 721)
(('NN', 'decrease'), 7)
(('NN', 'insider-trading'), 5)

ambiguous word example: 
('RB', 'back') 304
('VB', 'back') 20
('RP', 'back') 84
('JJ', 'back') 25
('NN', 'back') 29
('VBP', 'back') 4

Expected Output
transition examples: 
(('--s--', 'IN'), 5050)
(('IN', 'DT'), 32364)
(('DT', 'NNP'), 9044)

emission examples: 
(('DT', 'any'), 721)
(('NN', 'decrease'), 7)
(('NN', 'insider-trading'), 5)

ambiguous word example: 
('RB', 'back') 304
('VB', 'back') 20
('RP', 'back') 84
('JJ', 'back') 25
('NN', 'back') 29
('VBP', 'back') 4

Out[13] - 

Accuracy of prediction using predict_pos is 0.8889

  Expected Output
  Accuracy of prediction using predict_pos is 0.8889

Out[14] -  All tests passed 

Out[16] - 

A at row 0, col 0: 0.000007040
A at row 3, col 1: 0.1691
View a subset of transition matrix A
              RBS            RP           SYM        TO            UH
RBS  2.217069e-06  2.217069e-06  2.217069e-06  0.008870  2.217069e-06
RP   3.756509e-07  7.516775e-04  3.756509e-07  0.051089  3.756509e-07
SYM  1.722772e-05  1.722772e-05  1.722772e-05  0.000017  1.722772e-05
TO   4.477336e-05  4.472863e-08  4.472863e-08  0.000090  4.477336e-05
UH   1.030439e-05  1.030439e-05  1.030439e-05  0.061837  3.092348e-02

Expected Output

A at row 0, col 0: 0.000007040
A at row 3, col 1: 0.1691
View a subset of transition matrix A
              RBS            RP           SYM        TO            UH
RBS  2.217069e-06  2.217069e-06  2.217069e-06  0.008870  2.217069e-06
RP   3.756509e-07  7.516775e-04  3.756509e-07  0.051089  3.756509e-07
SYM  1.722772e-05  1.722772e-05  1.722772e-05  0.000017  1.722772e-05
TO   4.477336e-05  4.472863e-08  4.472863e-08  0.000090  4.477336e-05
UH   1.030439e-05  1.030439e-05  1.030439e-05  0.061837  3.092348e-02
 
Out[17] -  All tests passed

Out[19] -

View Matrix position at row 0, column 0: 0.000006032
View Matrix position at row 3, column 1: 0.000000720
              725      adroitly     engineers      promoted       synergy
CD   8.201296e-05  2.732854e-08  2.732854e-08  2.732854e-08  2.732854e-08
NN   7.521128e-09  7.521128e-09  7.521128e-09  7.521128e-09  2.257091e-05
NNS  1.670013e-08  1.670013e-08  4.676203e-04  1.670013e-08  1.670013e-08
VB   3.779036e-08  3.779036e-08  3.779036e-08  3.779036e-08  3.779036e-08
RB   3.226454e-08  6.456135e-05  3.226454e-08  3.226454e-08  3.226454e-08
RP   3.723317e-07  3.723317e-07  3.723317e-07  3.723317e-07  3.723317e-07

Expected Output
View Matrix position at row 0, column 0: 0.000006032
View Matrix position at row 3, column 1: 0.000000720
              725      adroitly     engineers      promoted       synergy
CD   8.201296e-05  2.732854e-08  2.732854e-08  2.732854e-08  2.732854e-08
NN   7.521128e-09  7.521128e-09  7.521128e-09  7.521128e-09  2.257091e-05
NNS  1.670013e-08  1.670013e-08  4.676203e-04  1.670013e-08  1.670013e-08
VB   3.779036e-08  3.779036e-08  3.779036e-08  3.779036e-08  3.779036e-08
RB   3.226454e-08  6.456135e-05  3.226454e-08  3.226454e-08  3.226454e-08
RP   3.723317e-07  3.723317e-07  3.723317e-07  3.723317e-07  3.723317e-07


Out[20] -  All tests passed

Out[23] - 

best_probs[0,0]: -22.6098
best_paths[2,3]: 0.0000

Expected Output

best_probs[0,0]: -22.6098
best_paths[2,3]: 0.0000

Out[24] -  All tests passed

Out[26] -

Words processed:     5000
Words processed:    10000
Words processed:    15000
Words processed:    20000
Words processed:    25000
Words processed:    30000

Out[27] -

best_probs[0,1]: -24.7822
best_probs[0,4]: -49.5601
 
 Expected Output
  best_probs[0,1]: -24.7822
  best_probs[0,4]: -49.5601

Out[28] -  All tests passed

Out[53] - 

The prediction for pred[-7:m-1] is: 
 ['see', 'them', 'here', 'with', 'us', '.'] 
 ['VB', 'PRP', 'RB', 'IN', 'PRP', '.'] 

The prediction for pred[0:8] is: 
 ['DT', 'NN', 'POS', 'NN', 'MD', 'VB', 'VBN'] 
 ['The', 'economy', "'s", 'temperature', 'will', 'be', 'taken']

Expected Output:

The prediction for pred[-7:m-1] is:  
 ['see', 'them', 'here', 'with', 'us', '.']  
 ['VB', 'PRP', 'RB', 'IN', 'PRP', '.']   
The prediction for pred[0:8] is:    
 ['DT', 'NN', 'POS', 'NN', 'MD', 'VB', 'VBN']   
 ['The', 'economy', "'s", 'temperature', 'will', 'be', 'taken']

Out[54] -  All tests passed

Out[55] - 

The third word is: temperature
Your prediction is: NN
Your corresponding label y is:  temperature	NN

Out[57] - 

Accuracy of the Viterbi algorithm is 0.9531

Expected Output
 
Accuracy of the Viterbi algorithm is 0.9531

Out[58] - All tests passed



 
<br/><br/>
</pre>
