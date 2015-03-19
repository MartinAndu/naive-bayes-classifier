# Naive Bayes Classifier #

"A Bayes classifier is a simple probabilistic classifier based on applying Bayes' theorem (from Bayesian statistics)" -- Wikipedia (http://en.wikipedia.org/wiki/Naive_Bayes_classifier)

This project contains source files that can be included in any c++ project. It uses data from simple text files and constructs a Naive Bayes classifier.

## How to use the Naive Bayes Classifier ##

The Naive Bayes Classifier needs data to function. The data is contained in a simple text file. Floats are used to represent every data.

In my example, the bayesianClassifierValues.txt contains the following values :

1.0 2.0 1.0<br />
2.0 1.0 1.0<br />
1.0 2.0 0.0<br />
0.0 0.0 0.0

**Beware, it won't work if there is an empty line at the end of the file**

The input or situation data is contained into the n - 1 first columns. In this case, it is the first and second column.
The output observed, depending on the situation, is the last column.

For example, these data contain situations observed by a Boss in a 2D scroller video game.

  * The first column is the relative position of his opponent on the X axis.
  * The second column is the relative position of his opponent on the Y axis.
  * The last column (the output) is a boolean representing if the opponent is hurt or not.

### Domains ###

A Bayes classifier needs discrete values to work properly. For each column, a domain has to be associated.
Each of these domains contains the minimum value, maximum value and the number of values between them.

For example, the domain for the first column (the relative position of the opponent on the X axis) is defined like this :

Domain(0.0, 2.0, 3)

The minimum value is 0.0 and maximum value is 2.0. The number of values between them are 3. Hence, the conversion is defined like this :

Discrete value = ((value - min) `*` number of values) / (max - min)

0 = ((0.5 - 0.0) `*` 3 / (2.0 - 0.0)<br />
1 = ((0.67 - 0.0) `*` 3 / (2.0 - 0.0)<br />
1 = ((1.32 - 0.0) `*` 3 / (2.0 - 0.0)<br />
2 = ((1.34 - 0.0) `*` 3 / (2.0 - 0.0)

Approximatively, the domains for the first column are these :

[0, 0.666[ = 0<br />
[0.666, 1.333[ = 1<br />
[1.333, 2.0[ = 2

Changing the number of values between the minimum and maximum can affect greatly the results. Test with multiple values for better results.

### Bayesian Classifier Class ###

The Bayesian Classifier is capable of calculating the most probable output depending of the input.
It is possible to add new raw data at runtime and have a better probabilistic classifier.

### Action Classifier Class ###

The Action Classifier is capable of calculating the best action to perform to obtain a certain output.
It is possible to add new raw data at runtime and have a better probabilistic classifier.

It uses the Bayesian Classifier but it has 1 more column. The data is still contained in a simple text file.

In my example, the actionClassifierValues.txt contains the following values :

1.0 2.0 1.0 1.0<br />
2.0 1.0 1.0 1.0<br />
1.0 2.0 0.0 0.0<br />
0.0 0.0 0.0 0.0

**Beware, it won't work if there is an empty line at the end of the file**

The input or situation data is contained into the n - 2 first columns. In this case, it is the first and second column.
The output observed depending on the situation is the n - 1 column or in this case, the third column.
The action done in this situation (input) obtaining the output is the last column.

For example, these data contain situations observed by a Boss in a 2D scroller video game.

  * The first column is the relative position of his opponent on the X axis.
  * The second column is the relative position of his opponent on the Y axis.
  * The third column (the output) is a boolean representing if the opponent is hurt or not.
  * The last column (the action) is a number representing the action done by the Boss. A C++ enumeration is recommended.

### Code Sample ###
```
	vector<Domain> domains;
	
	// The input domains
	domains.push_back(Domain(0.0, 2.0, 3));
	domains.push_back(Domain(0.0, 2.0, 3));
	// The ouput domain
	domains.push_back(Domain(0.0, 1.0, 2));
	
	BayesianClassifier classifier("/Users/simonlg/Projects/Personnal/NaiveBayesClassifier/Development/trunk/src/data/bayesianClassifierValues.txt", domains);

	// It is possible to not give any data and add it later
	// BayesianClassifier classifier(domains);
	
	vector<float> input1;
	
	input1.push_back(1.0);
	input1.push_back(2.0);
	
	// Calculates the probability of obtaining the output 0.0 given the input (1.0, 2.0)
	if(classifier.calculateProbabilityOfOutput(input1, 0.0) != 0.5) {
		cout << "The probability should be 0.5" << endl;
		errors++;
	}
	
	// Updates the classifier with new values (1.0, 2.0, 0.0)
	classifier.addRawTrainingData("/Users/simonlg/Projects/Personnal/NaiveBayesClassifier/Development/trunk/src/data/newBayesianClassifierValues.txt");
	
	if(classifier.calculateProbabilityOfOutput(input1, 0.0) <= 0.727272 || classifier.calculateProbabilityOfOutput(input1, 0.0) >= 0.727274) {
		cout << "The probability should be 0.727273" << endl;
		errors++;
	}
	
	// The most probable output should be 0.0 given the input (1.0, 2.0)
	if (classifier.calculateOutput(input1) != 0.0) {
		cout << "The output should be 0.0" << endl;
		errors++;
	}
	
	vector<float> newRawTrainingData;
	
	newRawTrainingData.push_back(1.0);
	newRawTrainingData.push_back(2.0);
	newRawTrainingData.push_back(1.0);
	
	classifier.addRawTrainingData(newRawTrainingData);

	if(classifier.calculateProbabilityOfOutput(input1, 0.0) <= 0.49 || classifier.calculateProbabilityOfOutput(input1, 0.0) >= 0.51) {
		cout << "The probability should be 0.5" << endl;
		errors++;
	}
	
	if (errors == 0) {
		cout << "TestBayesianClassifier passed" << endl;
	}
```