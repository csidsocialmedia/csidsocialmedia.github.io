---
title: Correcting Spelling PCIgroup
layout: post
guid:
comments: true
tags:
  - PCI group
---


### 1. Warm up and self-introduction 


Name, major, grade, programing experience, motivation to join our group/project at hand


### 2. About me

PowerPoint

### 3. My suggestions of learning data mining/machine learning (for non-CS students)


    1. Small projects driven;
	2. Do not talk about concepts/formulas unless you know how to turn them into codes;
	3. Do not optimize codes too early, ugly codes also get things done;
	4. When you can always get things done, try to write more elegant codes.
	


### 4. Check your developing evironment of Python (IPython/Pythonxy)


### 5. A spelling corrector in 22 lines of Python codes


The following code was wrote by [Peter Norvig](http://norvig.com/spell-correct.html) and was revised slightly by me.

	import re, collections, urllib2
	
    def words(text): return re.findall('[a-z]+', text.lower()) 
    
    def train(features):
        model = collections.defaultdict(lambda: 1)
        for f in features:
            model[f] += 1
        return model
		
    NWORDS = train(words(urllib2.urlopen('http://norvig.com/big.txt').read()))
    
    alphabet = 'abcdefghijklmnopqrstuvwxyz'
    
    def edits1(word):
       splits     = [(word[:i], word[i:]) for i in range(len(word) + 1)]
       deletes    = [a + b[1:] for a, b in splits if b]
       transposes = [a + b[1] + b[0] + b[2:] for a, b in splits if len(b)>1]
       replaces   = [a + c + b[1:] for a, b in splits for c in alphabet if b]
       inserts    = [a + c + b     for a, b in splits for c in alphabet]
       return set(deletes + transposes + replaces + inserts)
    
    def known_edits2(word):
        return set(e2 for e1 in edits1(word) for e2 in edits1(e1) if e2 in NWORDS)
    
    def known(words): return set(w for w in words if w in NWORDS)
    
    def correct(word):
        candidates = known([word]) or known(edits1(word)) or known_edits2(word) or [word]
        return max(candidates, key=NWORDS.get)


### 6. Python programming skills

The math, modules, functions, and data structures used in the above example

1. Math: Bayesian formula

![Bayes_Theorem](/media/files/2014-03-28-Correcting-Spelling-PCI-group/Bayes_Theorem.jpg)
[source](http://en.wikipedia.org/wiki/File:Bayes%27_Theorem_MMB_01.jpg)


2. Modules: re, collections, urllib2

3. Data structures: list, dictionary, default dictionary

4. Functions: basic usage, nest functions, for loop 


### 7. Q&A

