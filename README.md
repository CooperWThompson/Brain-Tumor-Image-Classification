# Brain Tumor Image Classification

![Movie Image](./images/brain_scan.jpeg)

# Overview
Cancer is one of the most ramp-it and devastating diseases in this world. One of the few things that humans can agree on, is everyone hates cancer. It is probably the biggest war that humanity has ever faced. Cancer has no empathy, and has no regard for pain and suffering. With that said, when I thought of what I wanted to do with my final project, I wanted to do something that mattered and had depth to it. When I learned that I could use image classification to determine if someone has a brain tumor, I jumped at the idea. My project in the grand scheme probably won't help cure cancer, but the fact that I can use neural networks and image classification, two of the most fascinating topics I have ever learned about, to work on a problem that is much greater than myself or this program was enough to get me pumped to start this project. In this project, I tackle a image classification problem that is idenifying if someone has a brain tumor or not. I attack this problem by building a convelusional neural network. After getting the model as accurate as possible, I will use the LIME library to evaluate why the model is making the prediction it is making. I will also talk about how this can be helpful in the world, and what would be the next few steps to continue this project.


# Business Problem

The business idea for this project, is to optomize Doctor's time by limiting the amount of MRI scans they have to look at. The idea is that someone comes in and gets a scan of their brain and it is immediately put through this model, and if the model believes they have a tumor, it will flag it for a doctor to look at, but if it is completely sure that they do not have a tumor then it will not require a doctor to look at it. Doing this greatly reduces the number of MRI scans that a doctor will have to look at. Allowing their precious time to be spent on something else. Bringing automation into a process like this does bring up a concern. The worst case senario is having the model predict someone doesn't have cancer when they actually do. In this context, that is a false negative. So it will be the upmost importance to reduce false negatives in this project.

# Data Understanding

The data that I used are MRI brain scans from Kaggle. In this data I have about 3,300 images, and originally the data had four classifications. There were three different types of tumors and then a no tumor classification. Since for this problem we only care if someone has a tumor or not, you will see below in the data preparation section I take the three different types of tumors and combine them into one category. This leaves us with two categories, "tumor" and "no tumor". This makes it a binary classification problem. After combining the those data points, I was left with a class imbalance of a lot more tumor images than no tumor images, and 3,300 also isn't the most data points. So with that said as you will see down in the modeling section, I use data augmentation to solve both of those. I go into more detail about data augmentation in the modeling section, but essentially it is both a good way to help me get more images as data and helps with the class imbalance. 

# Data Preparation

In this data preparation section, I had to get the data to a place that it could work with the neural network. The first part is just getting the data imported and labeled correctly. After that I run a train test split on the data. I do it a second time to create a hold out set to test the model on at the end. I get the data in numerical categories instead of strign categories. I then finally as mentioned before get the data into just tumor or no tumor. Data augmentation should also get a call out here, even though I do it in the modeling section later, because that is a step I do with the data. It is a way to get more data and it is covered more in the modeling section.

# Modeling

The first model I made was just a basic neural network that had one middle hidden layer of 64 nodes. For every epoch it was producing the exact same accuracy. This was due to our class imbalance and so it was just predicting tumor everytime. Next we tried another basic neural network but with more hidden layers and dropout layers to see if this helps. It still just predicting tumor every single time. This was evidence that I was gonna have to run a convolutional neural network instead. 

The next steps was to try a CNN (convoluntional neural netwrok) with dropout layers, and one with kernel regularizer. Both dropout layers and kernel regularizer are different ways to fight overfitting. Both made the model better, and it is no longer just predicting tumor every time. The CNN model using kernel regularizer was the best model so far, so I moved forward with that. 

The next step was to try a few models with different amount of layers and different number of nodes to try and find the best combination. After trying alot of combinations and different things, I couldn't get it higher or get rid of overfitting completly. So I decided to bring in a pretrained model for the CNN. I used resnet-50 as my pretrained model. I brought it in and used all the optimal layers and nodes I had found. On top of that I went back and added data augmentation. This essentially takes all of the images and turns them 10 degress both directions and changes the brightness of the images to essentially create new images for the network to learn from. This is the process that lead to my final model.

# Evaluation

## Evaluating the numbers
Time to evaluate the final model. I evaluated it on accuracy and recall score. The accuracy is important becasue this is just how often it gets the prediction right versus when it gets it wrong. The recall score is also important becasue it is the score that represents the false negatives. If you have a high score in this that means you are reducing false negatives and that is important in this The model scored really well. On the training data it got 99.4% accuracy, 0.28 loss, and a recall score of 99.7%. On the testing data, it got 98.5% accuracy, 0.33 loss, and a recall score of 99.1%. On the holdout set, it got 100% accuracy, 0.27 loss, and a recall of 100%. These are incredible numbers. The fact that it got 100% on the holdout set which is the data it has never seen before is incredible. Now it a very small sample size. but definitely a good sign moving forward. 

## Evaluating with LIME

In this section, I will be using the LIME library to look at and evaluate what the neural network is thinking. LIME allows us to visualize the parts of the image that is leading the neural network to make its predictions. Below are 2 images from the holdout set, and as you can see there is green and red markings on them. Where you see green on the images that is the part of the image that it is using to help make a prediction that is a tumor, and where you see red is the part of the image that is making it think there isn't a tumor.

![Movie Image](./images/good_lime_picture.png)

![Movie Image](./images/bad_lime_picture.png)

There are a few take aways from these images. The first thing to remember is that the model got the prediction right on both of these. As you can see the first image has almost the entire brain green which lead to its prediction of it having a tumor which was correct. With the second picture almost the whole brain is red which lead to a prediction of no tumor which was correct. Now you might be asking, why is it learning from all those random spots around the black background head, neck, mouth, or just anything that isn't the brain. This does bring up a good point though. Why is it learning from those random places or parts of the image that definitely shouldn't be learning from. For example, it is learning from the edges of the images that have nothing to do with the brain. This would definitely be a next step moving forward with this project. To try and figure out why it is learning from those random parts and how to limit that.

# Next Steps

Like I said above, an obvious next step would be to look into why the neural network is learning from parts of the image it shouldn't be learning from and to find ways to reduce that. Another good next step would be to do image localization and image segmentation. Image localization will put a box around where it thinks the tumor is, and image segmentation would actually outline exactly where it thinks the tumor is. This would be another way to draw the doctor's attention to that area and may even lead the doctor to see the tumor now when they wouldn't have before. Probably the best thing that can come from that is actaully just allow me to understand more what the model is thinking and might lead me to finding a better way to train the model. Another step moving forward which I believe to be the most important is thresholding. Right now the model has a threshold of 50%, which means if it thinks you have a 50+% chance of having a tumor it will predict tumor, but what this means is that if you have a 49% chance of having a tumor, it would say you don't have a tumor. This is not a great way to apply this model to the real world and definitely doesn't reduce false negatives like I said I wanted to do eariler. This would take a lot of testing to find the right number for the threshold to essentially eliminate all chances of false negatives.


# Conclusion


This model is incredibily accurate at predicting from an MRI brain scan if someone has a tumor or not. The parts of the image which it is learning from is a little peculiar and I would like to look into why it is doing that and how to reduce it learning from parts it shouldn't be. Also a very important step in this is that the model should not be put into pratice until the threshold has been looked at and studied to find the right number to reduce false negatives as much as humanly possible.