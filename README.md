# Software Introduction
This Java package corresponds to my research paper as follows:<br />
**Yang Qian, Junming Yin, Yezheng Liu, Yuanchun Jiang. TopicModel4J: A Java Package for Topic Models, to be submitted.**<br />
<br />
This package is about Topic Models for Natural Language Processing (NLP). And **it provides an easy-to-use interface for researchers and data analysts**.<br />

Motivations：I develop this Java package to promote related research about Topic Models for Natural Language Processing (NLP). <br />

When submitting my research paper to a journal, I will publicly release all the source code.<br />

# Jar Dependency
If you want to use this package, you need download some Java jars: commons-math3-3.5.jar, lingpipe-4.1.0.jar, stanford-corenlp-3.9.1-models.jar, stanford-corenlp-3.9.1-sources.jar, stanford-corenlp-3.9.1.jar. The stanford-corenlp 3.9.1 can be download from this website: http://central.maven.org/maven2/edu/stanford/nlp/stanford-corenlp/3.9.1/.

# Data Preprocessing for NLP
This software can do the following text preprocessing:<br />
* (1) Split the sentence to words.<br />
* (2) Lowercase the words and preform lemmatization.<br />
* (3) Remove useless characters, URLs and stop words.<br />

The first example is as follows:<br />
```java
import java.util.ArrayList;
import com.topic.utils.FileUtil;
public class RawDataProcess {
	/**
	 * Functions:
	 * 
	 * (1) Split the sentence to words
	 * (2) Lowercase the words and preform lemmatization
	 * (3) Remove special characters (e.g., #, % and &), URLs and stop words
	 * 
	 * @author: Yang Qian
	 */
	public static void main(String[] args) {
			String line = "http://t.cn/RAPgR4n Artificial intelligence is a known phenomenons "
					+ "in the world today. Its root started to build years "
					+ "ago but the tree started to grow long after. Months ago when our beloved google assistant made her first "
					+ "call to book a haircut appointment in the Google IO event,";
			//get all word for a document
			ArrayList<String> words = new ArrayList<String>();
			//lemmatization using StanfordCoreNLP
			FileUtil.getlema(line, words);
			//remove noise words
			String text = FileUtil.RemoveNoiseWord(words);
			System.out.println(text);
	}
}
```
Running this code, we can obtain the following results:<br />
```java
artificial intelligence phenomenon world today root start build year ago tree start grow long month ago beloved google assistant make call book haircut appointment Google IO event
```
If we want deal a file which a line represent one document. For example,
```java
We present a new algorithm for domain adaptation improving upon a discrepancy minimization algorithm, (DM), previously shown to outperform a number of algorithms for this problem. 
We investigated the feature map inside deep neural networks (DNNs) by tracking the transport map. We are interested in the role of depth--why do DNNs perform better than shallow models?
```
We denote this file as 'rawdata'. And we can use the next code to deal with:
```java
import java.io.IOException;
import java.util.ArrayList;
import com.topic.utils.FileUtil;

public class RawDataProcessing {
	/**
	 * Functions:
	 * 
	 * (1) Split the sentence to words
	 * (2) Lowercase the words and preform lemmatization
	 * (3) Remove special characters (e.g., #, % and &), URLs and stop words
	 * 
	 * @author: Yang Qian
	 */
	public static void main(String[] args) throws IOException {
		//read data
		ArrayList<String> docLines = new ArrayList<String>();
		FileUtil.readLines("data/rawdata", docLines, "gbk");
		ArrayList<String> doclinesAfter = new ArrayList<String>();
		for(String line : docLines){
			//get all word for a document
			ArrayList<String> words = new ArrayList<String>();
			//lemmatization using StanfordCoreNLP
			FileUtil.getlema(line, words);
			//remove noise words
			String text = FileUtil.RemoveNoiseWord(words);
			doclinesAfter.add(text);
		}
		// write data
		FileUtil.writeLines("data/rawdata_process", doclinesAfter, "gbk");
	}
}
```

# Algorithm for NLP
The algorithms in this package contain **Latent Dirichlet Allocation (LDA), Biterm Topic Model (BTM),  Author-topic Model (ATM), Dirichlet Multinomial Mixture Model (DMM), Dual-Sparse Topic Model (DSTM), Labeled LDA, Link LDA, Sentence-LDA, Pseudo-document-based Topic Model (PTM), Hierarchical Dirichlet processes, Collaborative topic Model (CTM), Gaussian Lda and so on **. Now, I will intorduce how to use my package for running some algorithms.

## Latent Dirichlet Allocation (Collapsed Gibbs sampling)
Reference: (1) Griffiths T. Gibbs sampling in the generative model of latent dirichlet allocation[J]. 2002.<br />
           (2) Heinrich G. Parameter estimation for text analysis[R]. Technical report, 2005.<br />
The following code is to call the LDA algorithm for processing text:<br />
```java
import com.topic.model.GibbsSamplingLDA;

public class LDAGibbsSamplingTest {

	public static void main(String[] args) {
		GibbsSamplingLDA lda = new GibbsSamplingLDA("data/rawdata_process_lda", "gbk", 50, 0.1,
				0.01, 500, 50, "data/ldaoutput/");
		lda.MCMCSampling();

	}
}
```
Where the constructor method GibbsSamplingLDA() is:
```java
public GibbsSamplingLDA(String inputFile, String inputFileCode, int topicNumber,
			double inputAlpha, double inputBeta, int inputIterations, int inTopWords,
			String outputFileDir)
```
The input file ('rawdata_process_lda') contains many document, like: <br />

![input file](https://img-blog.csdnimg.cn/2019060820040440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xaWFueWFuZy1oZnV0LmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70#pic_center)

Running the LDAGibbsSamplingTest.java, we can obtain the result after some iterations. <br />
![Running](https://img-blog.csdnimg.cn/20190608200759730.png#pic_center)

The outfile contains 'LDAGibbs_topic_word_50.txt' and 'LDAGibbs_doc_topic50.txt'. The content of 'LDAGibbs_topic_word_50.txt' likes: <br />
```java
Topic:0
study :0.03364301916742469
student :0.029233711281785802
online :0.01600578762486915
game :0.01502594142806051
teacher :0.012739633635507014
social :0.01192309513816648
activity :0.010453325842953519
examine :0.01029001814348541
technology :0.00980009504508109
...

Topic:1
fuzzy :0.07505158709641029
method :0.031024330934552934
decision :0.02585387024650563
criterion :0.021780173946831995
propose :0.021310132066100423
base :0.017706477647158363
number :0.016609713258784693
problem :0.015982990751142595
uncertainty :0.013632781347484729
set :0.012692697586021583
make :0.012536016959111058
paper :0.012379336332200534
risk :0.011752613824558436
...
```

##  Latent Dirichlet Allocation (Collapsed Variational Bayesian Inference)
We also use Collapsed Variational Bayesian Inference (CVBI) for learning the parameters of LDA. <br />
Reference: (1)Teh Y W, Newman D, Welling M. A collapsed variational Bayesian inference algorithm for latent Dirichlet allocation[C]//Advances in neural information processing systems. 2007: 1353-1360. <br />
(2)Asuncion A, Welling M, Smyth P, et al. On smoothing and inference for topic models[C]//Proceedings of the twenty-fifth conference on uncertainty in artificial intelligence. AUAI Press, 2009: 27-34. <br />

The following code is to call the algorithm for processing text:<br />
```java
import com.topic.model.CVBLDA;

public class CVBLDATest {

	public static void main(String[] args) {
		CVBLDA cvblda = new CVBLDA("data/rawdata_process_lda", "gbk", 30, 0.1,
				0.01, 200, 50, "data/ldaoutput/");
		cvblda.CVBInference();
	}
}
```
Where the constructor method GibbsSamplingLDA() is:
```java
public CVBLDA(String inputFile, String inputFileCode, int topicNumber,
			double inputAlpha, double inputBeta, int inputIterations, int inTopWords,
			String outputFileDir)
```
Running the CVBLDATest.java, we can obtain the result liking LDAGibbsSamplingTest.java. <br />

## Labeled LDA
We use gibbs sampling for implementing the Labeled LDA algorithm. <br />
Reference:Ramage D, Hall D, Nallapati R, et al. Labeled LDA: A supervised topic model for credit attribution in multi-labeled corpora[C]//Proceedings of the 2009 Conference on Empirical Methods in Natural Language Processing: Volume 1-Volume 1. Association for Computational Linguistics, 2009: 248-256.<br />

The following code is to call the Labeled LDA algorithm for processing text:<br />
```java
import com.topic.model.LabeledLDA;

public class LabeledLDATest {

	public static void main(String[] args) {
		LabeledLDA llda = new LabeledLDA("data/rawdata_process_author", "gbk", 0.1,
				0.01, 500, 50, "data/ldaoutput/");
		llda.MCMCSampling();
	}
}
```
Where the constructor method LabeledLDA() is:
```java
public LabeledLDA(String inputFile, String inputFileCode,
			double inputAlpha, double inputBeta, int inputIterations, int inTopWords,
			String outputFileDir)
```
The input file ('rawdata_process_author') contains many document with labels, like: <br />
```java
457720--578743--643697--840908--874627--975162--1058302--1275106--1368496--1769120--1769130--2135000	paper present indoor navigation range strategy monocular camera exploit architectural orthogonality indoor environment introduce method estimate range vehicle state monocular camera visionbased SLAM navigation strategy assume indoor indoorlike manmade environment layout previously unknown gpsdenied representable energy base feature point straight architectural line experimentally validate propose algorithm fully selfcontained microaerial vehicle mav sophisticated onboard image processing slam capability building enable small aerial vehicle fly tight corridor significant technological challenge absence gps signal limited sense option experimental result show systemis limit capability camera environmental entropy
273266--1065537--1120593--1474359--1976664--2135000	globalisation education increasingly topic discussion university worldwide hand industry university leader emphasise increase awareness influence global marketplace skill graduate time emergence tertiary education export market prompt university develop international recruitment strategy offer international student place undergraduate graduate degree programme article examine phenomenon globalisation emergence global intercultural collaboration delivery education effort global intercultural collaboration offer institution student learn successful approach
```
Where the label and the document are segmented by '\t'. The label can be String character.
Running the LabeledLDATest.java, we can output two files (LabeledLDA_topic_word.txt and LabeledLDA_doc_topic.txt). <br /> 
The contents of 'LabeledLDA_topic_word.txt' like: <br /> 
```java
Topic:1
system :0.008885972224685621
car :0.008885972224685621
mf :0.007112325074049769
stalk :0.0053386779234139165
speed :0.0053386779234139165
year :0.0053386779234139165
...

Topic:175
residual :0.017458207100978618
lease :0.015278655652666681
cash :0.015278655652666681
plan :0.013099104204354743
car :0.010919552756042806
price :0.00874000130773087
texas :0.00874000130773087
buy :0.006560449859418931
...
```








