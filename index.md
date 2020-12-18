## Convolutional Neural Networks to Predict the existance of COVID-19 using EKG

### Abstract

Given the rapid rise of the COVID-19 pandemic, lots of work has gone into devising sensitive and specific tests to detect the presense of COVID-19. There remains several open questions in creating rapid tests to determine disease severity, which would be a useful determinant of which patients to prioritize at intake in a hospital setting, and which patients are likely to need critical care. Using EKG data & corresponding viral loads from a pilot study at UW Medicine, I attempted to use LSTMs and CNNs to predict COVID-19 severity. Upon critical examination of the data and discovering low fidelity ground truth labels, I pivoted to creating a pretrained model based on control samples and used a CNN to predict whether a given patient has COVID-19 based on a 5 second sample from their EKG. This was highly effective, test accuracy was 97%.  

### Problem Statement: 
Using a patient's EKG, can we detect whether they have COVID? 

### Background & Dataset:

Prior work suggests that a patient's EKG, ie the electrical signal from their heart, might be affected by the COVID-19 virus. In early 1999, biology researchers injected 31 rabbits with RbCV, a different coronavirus, and examined their serial EKGs by hand, measuring heart rate; P-R interval; QRS duration; QTc interval; and P-, QRS-, and T-wave voltages. The study concluded that "ECG changes observed during RbCV infection are similar to the spectrum of interval/segment abnormalities, rhythm disturbances, conduction defects, and myocardial pathology seen in human myocarditis, heart failure, and dilated cardiomyopathy. [1]" Given this information, and examining notable literature using signal processing and deep learning methods on EKGs [2, 3], an inherently time-dependent signal, leads us to consider using deep learning on the EKGs to detect COVID. 


There were 2 primary corpuses of data colleted during a collaboration between AliveCorr, a portable EKG company (https://www.alivecor.com/kardiamobile6l/), and UW Medical Center.

The Control Dataset was X instances of data collected in 2019 from a total of Y patients. Each of the patients has between 1 and 8 distinct EKGs in the dataset. 

The Covid Dataset was 1750 instances of data collected after March of 2020. 130 patients were diagnosed with COVID at the UW Medical Center, and each was sent home with a portable EKG machine. They were asked to self-administer both a COVID viral load test (a nasal swab) and the 30-second EKG using the AliveCorr device for 14 days proceeding their initial COVID positive result. 

Here is an example of the viral loads over 14 days:

<img src="./patient1.png" width="250"> <img src="./patient2.png" width="250"> <img src="./patient3.png" width="250">

As you might observe, not all patients have all 14 days of data, some have missing days in the middle of their progressions. 

The EKG data itself for each patient is a 9000 timestep EKG taken over a 30 second period. The signals look as follows: 

![Image](./ekg_samples.png)

The peculiar thing about EKGs is that they're similar to a human fingerprint. No two EKGs look the same, and hence any shift that might be cause by COVID-19 on the EKGs will look different from patient to patient.

### Methodology - what is your approach/solution/what did you do?

#### Data Cleaning & Initial Results
This project began with the objective of detecting the exact viral load of a patient (recorded in the COVID-19 dataset as a result of the nasal swab. The viral loads themselves range between 15 and 45 and in our dataset, they are a semiquantitative measure that represents the number of cycles needed to detect the virus. A viral load of 0 indicates no presense of the virus, whereas a viral load of > 0 indicates that the virus is detectible within the sample. 

Here is the distribution of the viral loads:

![Image](./viral_dist.png)

In order to predict viral load from the EKG, I took the  samples of length 9000, normalized them, split them into 5 second chunks (ie 1500 timesteps per instance) and then ran a baseline neural network. 

<img src="./cnn.png" width="250">

After training for 100 epochs, this network achieved validation loss of 0.3467, ie each prediction was 0.54 off from the actual target. This seemed way too high! Also, the number of parameters in comparison to the number of training samples meant that I'd overfit on the training data.

Given the timeseries nature of the EKG (a signal) I realized that an LSTM might be able to capture the signal more accurately. Hence I ran a model to predict viral load using the following simple LSTM, which had far fewer parameters.

<img src="./lstm.png" width="250">

This resulted in a test mean squared error loss of 0.14555, meaning that the predictions were 0.37 off on average. Since the results were poor, for sanity's sake I ran a COVID/not COVID classifier using the COVID-19 dataset. Doing so yeilded a result that was worse that 50%. Given the binary nature of the classification task, this tipped me off that something was inherently wrong with our dataset. 

#### Further examination of the data:

This happened to be around the time of the Andrej Karpathy lecture, and as per his advice I took a deep look at the dataset and realized that lots of patients would have a high viral load between days 1-4, then the viral load would drop to 0 for a few days, after which the viral load would again rise above 0 (this can be seen in the above images of patients' viral loads). 
This seems to indicate 1 of 3 outcomes:
- the patients were not adequately compliant with the guidelines on how to carry out the nasal swab, or 
- the patients were compliant, but the sensitivity of the test was too low to detect the virus on those given days (false negative)
- or finally, that the virus itself ebbs and flows in it's severity over the 14 day period

#### Pretrained Model:
Given the nature of the data and how I couldn't be certain about the labeling, I decided to reevaluate my approach. Using a seperate corpus of data (described above as the control sample) I created a pretrained model that predicted Age from EKGs. Given that age is a high fidelity label that we had access to, this method gave the model a good way to learn about EKG data in general. I tried a variety of model architectures for the base model, and froze the bottom layers to retrain a head on the COVID-19 data in order to predict whether a patient had COVID or didn't have COVID.

### Experiments/evaluation - how are you evaluating your results
### Results - How well did you do
### Examples - images/text/live demo, anything to show off your work

### Video - a 2-3 minute long video where you explain your project and the above information


**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)

### References:

1. Alexander LK;Keene BW;Yount BL;Geratz JD;Small JD;Baric RS; “ECG Changes after Rabbit Coronavirus Infection.” Journal of Electrocardiology, U.S. National Library of Medicine, 1999, pubmed.ncbi.nlm.nih.gov/10037086/.  
2. Adedinsewo, Demilade, et al. “Artificial Intelligence-Enabled ECG Algorithm to Identify Patients With Left Ventricular Systolic Dysfunction Presenting to the Emergency Department With Dyspnea.” Circulation: Arrhythmia and Electrophysiology, 4 Aug. 2020, www.ahajournals.org/doi/10.1161/CIRCEP.120.008437. 
3. Attia, Zachi I., et al. “Screening for Cardiac Contractile Dysfunction Using an Artificial Intelligence–Enabled Electrocardiogram.” Mayo Clinic, Nature , 1 Jan. 2019, mayoclinic.pure.elsevier.com/en/publications/screening-for-cardiac-contractile-dysfunction-using-an-artificial. 
