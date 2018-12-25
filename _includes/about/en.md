> When you are about to give up, think of that one reason why you wanted to start.

## Introduction

I am Zhan Cheng, a graduate student pursuing master degree of Computer Science at [University of California, Irvine](https://uci.edu/). And I earned my bachelor degree of Computer Science and Technology at [University of Science and Technology of China (USTC)](http://en.ustc.edu.cn). 

I worked as an Algorithm Research Intern at [Tencent](https://www.tencent.com/en-us/index.html) YOUTU Lab for 3 months during my senior year. Most of my responsibilities are closely related to Computer Vision and Data Engineering. 

I worked as a research assistant in the NLP subgroup of the Jia Jia (a human-like robot) group inside the [Multi-Agent Systems Lab](http://ai.ustc.edu.cn/en/home/index.php) at USTC from my sophomore year under the supervision of [Prof. Xiaoping Chen](http://ai.ustc.edu.cn/en/people/xpchen.php). From Jun. 2017 to Aug. 2017, I did my summer research at [University of Waterloo](https://uwaterloo.ca) under the supervision of [Prof. Ming Li](https://cs.uwaterloo.ca/~mli/). I also led a small research team to work on a project about Knowledge Base Construction under the supervision of [Dr. Yi Zhou](https://staff.scm.uws.edu.au/~yzhou/).

## My Projects

### 1. Improving Humanoid Robot Jia Jia

**[Mentor: Xiaoping Chen](http://ai.ustc.edu.cn/en/people/xpchen.php)** ![](http://ai.ustc.edu.cn/images/xpchen.jpg)

*Professor in School of Computer Science and Technology at University of Science and Technology of China (USTC)*

**WHAT DID I DO:**

***Enhanced the robot’s Chinese Conversation System to improve its performance of communicating with human beings.***

Pictures of Jia Jia ![]({{site.baseurl}}/img/projects/jiajia00.JPG)

In order to make Jia Jia speak Chinese fluently and have a good performance when communicating with human beings, we need to do a lot of things like designing the conversation structure, figuring out how to do the dialogue management work, applying your model to real robot enviroment like ROS and also the tedious work of testing the robot and changing your code repeatedly.

While doing these things with other group members, I have really learned a lot of theories and methods behind making a robot act like a well-spoken person.I am really happy that I can contribute to the achievements of Jia Jia.

***Applied machine learning algorithms to the Question Classification Model of Jia Jia.***

![](http://upload-images.jianshu.io/upload_images/7248047-6ee602c694a4d0d1.png?imageMogr2/auto-orient/strip)

Question Classification Model is quite an important part of Jia Jia. A chat robot need to grasp the speaker's intention and decide to change this intention to real action sometimes merely from his short question.

I mainly work with the people in the NLP subgroup inside the Jia Jia group. With the help of them, I did a lot of research in the field of text classification and applied these algorithms to the Question Classification Model of Jia Jia. From Naive Bayes to SVM, then to deep learning model like CNN and RNN, I found the most suitable and practical method and used it to enhance the classification model. 

***Doing some tasks like building a WeChat version of Jia Jia, labeling data, etc.***

![]({{site.baseurl}}/img/projects/chatbot.png)

Sometimes, I did some simple tasks in the whole Jia Jia group like building a WeChat version of Jia Jia, labeling data, etc.

For instance, our wechat chatbot version of Jia Jia is mainly based on the original conversation system interface of Jia jia and the changed version of the code showed in the picture above.Learning new things always seems exciting for me.

**SOME EVENTS ABOUT JIA JIA:**

*Jia Jia meeting the former British Prime Minister Cameron*

![]({{site.baseurl}}/img/projects/jiajia01.JPG)

*Jia Jia meeting the well-known futurist Kevin Kelly*

![]({{site.baseurl}}/img/projects/jiajia04.JPG)

### 2. English Chatbot Completion Using HRED Model - Summer Research

**[Mentor: Ming Li](https://cs.uwaterloo.ca/~mli/)**  ![]({{site.baseurl}}/img/projects/lm.jpeg)

*Canada Research Chair in Bioinformatics                                                                    University Professor, School of Computer Science, University of Waterloo*

**WHAT DID I DO:**

***Built and improved the Hierarchical Recurrent Encoder-Decoder (HRED) model for English chatbot referring to some papers and code.***

Structure of Recurrent Encoder-Decoder (HRED) model

![]({{site.baseurl}}/img/projects/hred.png)

Chatbot is one important part of my mentor's company RSVP. Some of its current dialog systems is built on RNN model. However, simple RNN model is not strong enough to deal with many rounds of dialogue. In contrast, the Hierarchical Recurrent Encoder-Decoder model can improve its performance by introducing another interface beteewn encoder and decoder. And it has been proved that applying Attention Mechanism to the model will definitely help.

***Collected tens of millions of data from different sources like twitter, reddit, movie dialog, etc to train the RNN model.***

![]({{site.baseurl}}/img/projects/data01.png)

We all know that large amounts of data is essential to the trainning of the RNN model. So I spent a lot of time looking for and crawling the data I needed to train my model. Both the quantity and the quality of the data are important.

![]({{site.baseurl}}/img/projects/data02.png)

![]({{site.baseurl}}/img/projects/data03.png)

***Modified the code and the trainning sets repeatedly according to the performance of the chatbot until it achieved a desired result.***

Chatbot Dou Dou on Times Square's screen

![]({{site.baseurl}}/img/projects/doudou00.JPG)

I am still working on this task. The basic performance of my English chatbot is ok but not that satisfactory. So I need to modify the code or even the structure to get a better performance. It was also observed that simple HERD model still suffer from the problem of favoring generic utterances such as “I don't understand what you mean”, suggesting that HERD model may lack the inductive bias to faithfully represent the full diversity of complex utterances. We know that both the quantity and the quality of the data are important. But we also need to focus on structure and algorithms. So I did some extra surveys on Generative adversarial network (GAN) and Deep Reinforcement Learning. Some papers such as Generating Sentences by Editing Prototypes have also given me some ideas on this problem. So I am still working hard to hope that I can help Dou Dou become a more smart robot.
