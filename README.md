# mt4nltk

Goal: Improving MT capabilities in NLTK towards generating a phrase-table.

Contributors:
 


Day 1: Getting Started
====

Refer to http://docs.python-guide.org/en/latest/dev/virtualenvs/ to setup a virtual environment.

**TL;DR**

```
pip install virtualenv
mkdir mt4nltk
cd mt4nltk
virtualenv -p /usr/bin/python2.7 venv
source venv/bin/activate
git clone https://github.com/nltk/nltk.git
python setup.py
```

**If in Windows**:

http://arunrocks.com/guide-to-install-python-or-pip-on-windows/

```
# Start Powershell
Import-Module BitsTransfer 
Start-BitsTransfer -source https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
Start-BitsTransfer -source https://raw.github.com/pypa/pip/master/contrib/get-pip.py
setx path "%path%,C:\Python27;C:\Python27\Scripts\"
[Environment]::SetEnvironmentVariable("Path", "$env:Path;C:\Python27\;C:\Python27\Scripts\", "User")
python .\ez_setup.py
python .\get-pip.py
```



Familarize yourself with align and MT functions 
====

**The data structures available now in NLTK**:

```
>>> from nltk.align import AlignedSent
>>> algnsent = AlignedSent(['klein', 'ist', 'das', 'Haus'], ['the', 'house', 'is', 'small'], '0-2 1-3 2-1 3-0')
>>> algnsent.words
['klein', 'ist', 'das', 'Haus']
>>> algnsent.mots
['the', 'house', 'is', 'small']
>>> algnsent.alignment
Alignment([(0, 2), (1, 3), (2, 1), (3, 0)])
```

**How to call IBM models in NLTK?**:

```
from nltk.align import IBMModel2
bitext = []
bitext.append(AlignedSent(['klein', 'ist', 'das', 'haus'], ['the', 'house', 'is', 'small']))
bitext.append(AlignedSent(['das', 'haus', 'ist', 'ja', 'groß'], ['the', 'house', 'is', 'big']))
bitext.append(AlignedSent(['das', 'buch', 'ist', 'ja', 'klein'], ['the', 'book', 'is', 'small']))
bitext.append(AlignedSent(['das', 'haus'], ['the', 'house']))
bitext.append(AlignedSent(['das', 'buch'], ['the', 'book']))
bitext.append(AlignedSent(['ein', 'buch'], ['a', 'book']))
ibm2 = IBMModel2(bitext, 5)
```

**Example of GDFA and phrase extraction**:

```
$ python
>>> from nltk.align import phrase_based as pb
>>> srctext = "michael assumes that he will stay in the house"
>>> trgtext = "michael geht davon aus , dass er im haus bleibt"
>>> alignment = [(0,0), (1,1), (1,2), (1,3), (2,5), (3,6), (4,9), (5,9), (6,7), (7,7), (8,8)]
>>> phrases = pb.phrase_extraction(srctext, trgtext, alignment)
>>> for i in phrases:
...     print i
... 
```

You should see the output:

```
((2, 3), (5, 6), 'that', 'dass')
((1, 3), (1, 6), 'assumes that', 'geht davon aus , dass')
((2, 4), (5, 7), 'that he', 'dass er')
((1, 2), (1, 4), 'assumes', 'geht davon aus')
((4, 9), (7, 10), 'will stay in the house', 'im haus bleibt')
((0, 2), (0, 4), 'michael assumes', 'michael geht davon aus ,')
((4, 6), (9, 10), 'will stay', 'bleibt')
((1, 2), (1, 4), 'assumes', 'geht davon aus ,')
((0, 3), (0, 6), 'michael assumes that', 'michael geht davon aus , dass')
((3, 4), (6, 7), 'he', 'er')
((0, 4), (0, 7), 'michael assumes that he', 'michael geht davon aus , dass er')
((0, 1), (0, 1), 'michael', 'michael')
((0, 9), (0, 10), 'michael assumes that he will stay in the house', 'michael geht davon aus , dass er im haus bleibt')
((1, 4), (1, 7), 'assumes that he', 'geht davon aus , dass er')
((8, 9), (8, 9), 'house', 'haus')
((6, 9), (7, 9), 'in the house', 'im haus')
((2, 9), (5, 10), 'that he will stay in the house', ', dass er im haus bleibt')
((0, 2), (0, 4), 'michael assumes', 'michael geht davon aus')
((2, 3), (5, 6), 'that', ', dass')
((1, 9), (1, 10), 'assumes that he will stay in the house', 'geht davon aus , dass er im haus bleibt')
((6, 8), (7, 8), 'in the', 'im')
((2, 9), (5, 10), 'that he will stay in the house', 'dass er im haus bleibt')
((2, 4), (5, 7), 'that he', ', dass er')
((3, 9), (6, 10), 'he will stay in the house', 'er im haus bleibt')
```
----

Day 2: Getting down to work
====

So after the warm exercises, there's a few things that we can work on in parallel
 
**Pythonic Work**:
 
 - Get phrase-extraction code to take in an `AlignedSent` object instead of the current input parameters and create an output object call `AlignedPhrases` (make the abstract objects in `nltk.align.api`)
 - GDFA currently takes in strings, change it to take `AlignedSent` instead
   -  Solution 1: Make GDFA take 2 `AlignedSent` (one forward and one backwards) and then return a single `AlignedSent` object with GDFA-ed alignments (https://github.com/nltk/nltk/blob/develop/nltk/align/gdfa.py)
   -  Solution 2: Make an extract variable in `AlignedSent` call `AlignedSent.backwards_alignments` where it stores the backwards alignments.
   
 

**Phrase table Probabilities assignment**:

 - GDFA: http://www.statmt.org/moses/RELEASE-3.0/models/de-en/model/aligned.1.grow-diag-final-and
 - DE: http://www.statmt.org/moses/RELEASE-3.0/models/de-en/corpus/europarl.clean.1.de
 - EN: http://www.statmt.org/moses/RELEASE-3.0/models/de-en/corpus/europarl.clean.1.en
 
**Lexical reordering training**:

 - Build a function that learns a lexical reordering model.



Now let's start forking the code from NLTK and then git clone our own repository:

 - First create an account on [Github](https://github.com/) (if you haven't have one)
 - Then, go to https://github.com/nltk/nltk and fork the NLTK repo into your own github
 - Then go to your github NLTK repo page: https://github.com/<your_username>/nltk
 - Copy the clone URL
 - Now let's reset the environment and then git checkout the new branch or if it's TL;DR, then follow the instructions below
 

```
pip install virtualenv
mkdir mt4nltk
cd mt4nltk
virtualenv -p /usr/bin/python2.7 venv
source venv/bin/activate
git clone https://github.com/<your_username>/nltk.git
python setup.py
```

----

