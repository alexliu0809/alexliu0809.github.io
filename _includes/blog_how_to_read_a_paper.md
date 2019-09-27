# How To Read Scientific Literature
References:
* [How to Read a Paper](https://web.stanford.edu/class/ee384m/Handouts/HowtoReadPaper.pdf)
* [Efficient Reading of Papers in Science and Technology](https://www.cs.columbia.edu/~hgs/netbib/efficientReading.pdf)
* [How to Read a Computer Science Research Paper](https://people.cs.pitt.edu/~litman/courses/cs2710/papers/howtoreadacspaper.pdf)
* [How to Read a Technical Paper](https://www.cs.jhu.edu/~jason/advice/how-to-read-a-paper.html)

## Why Paper Reading?
Researchers must read papers for several reasons: to review them for a conference or a class, to keep current in their field, or for a literature survey of a new field.

## Three-Pass Approach
You should read the paper multiple passes, instead of starting at the beginning and plowing all the way to the end.

### First Pass
The purpose of first pass is to have a general idea about the paper. More specifically, you should:
* **Carefully** read the title, abstract, and introduction
* Read the section and sub-section headings, but ignore everything else
* Read the conclusions
At the end of the first pass, you should be able to answer the following questions:
* Category: What type of paper is this? Theoratical (describes a theory or algorithm or provides a mathematical proof)? Engineering (an implementation && evauluation of an algorithm, or part or all of a computer system or application)? Empirical paper (experiment designed to test some hypothesis)?
* Topic: What problems does this paper address? Are they important and why?
* Context: Which other papers is it related to? Which theoretical bases were used to analyze the problem?
* Contributions: What are the paper’s main contributions?
* Correctness: Do the assumptions appear to be valid?
Depending on this information, you might choose not to keep reading. This could because it is not interesting, the assumptions are not valid, or you don't have enough background knowledge.

### Second Pass
Grasp the paper's content, but not the detail. In the second pass, read the paper with greater care, but ignore details such as proofs. It helps to jot down the key points, or to make comments in the margins, as you read.
* Read the introductions carefully.
	* What assumptions do they rely on?
	* Do the authors present evidence that they know why they are doing this piece of research?
	* Do they have an idea of the larger picture?

* Read the definitions and theroms carefully.
* Read the methods section carefully
* Pay special attention to graphs. Are the axes properly labeled? Are results shown with error bars, so that conclusions are statistically significant?
* Mark relevant unread references for further reading. 
After the second pass, you should be able to summarize the main thrust of the paper, with supporting evidence, to someone else.

### Third Pass
The key to the third pass is to attempt to virtually re-implement the paper: that is, making the same assumptions as the authors, re-create the work. You should identify and challenge every assumption in every statement.

## Doing Literature Survey
* Use Google Scholar to find three to five **recent papers** in the area. Do one pass on each paper to get a sense of the work, then read their related work
sections. You will find a thumbnail summary of the recent work, and perhaps, if you are lucky, a pointer to a recent survey paper.
* Otherwise, in the second step, find shared citations and repeated author names in the bibliography. These are the key papers and researchers in that area. Download the key papers and set them aside. 
* The third step is to go to the website for these top conferences and look through their recent proceedings.

### Indicators of A Good Research Paper
* The problem the paper addresses is clearly stated, both in the abstract and early on in the paper itself. The technical importance and broader impacts of the paper are described.
* The paper includes a clear description of the experiment, system or theory the
problem addresses. This is usually the second section of the paper.
* The paper describes and analyzes the results of the work described. 
* The authors have some sound, non-trivial ideas for future work.

## Questions To Ask When Reading A Paper
### Assumptions
* Do their results rely on any assumptions about trends or environments?
* Are the author’s claims reasonable and realistic?

### Methods
* Is the approach clearly described? Can you outline the steps or summarize the approach?
* Did they address the problem stated earlier in the paper/measure what they claim?
* Can they explain what they observed?
* Did they have adequate controls/require unreasonable amounts of human guidance?
* Were tests carried out in a standard/objective way?

### Conclusions
* Do the conclusions follow logically from the observations?
* What other conclusions or correlations are there in the data that they did not point out?


