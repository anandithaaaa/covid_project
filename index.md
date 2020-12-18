## Welcome to My Deep Learning Class Project!

### Abstract - in a paragraph or two, summarize the project 

Using data from a novel study that collected EKGs for 14 days after a patient was diagnosed with COVID, I apply LSTMs and CNNs to predict whether the patient has COVID-19. There is reason to believe that the 

### Problem statement: Using a patient's EKG, can we detect whether they have COVID? 
### Dataset: what papers/ideas inspired you, what datasets did you use, etc

There were 2 primary corpuses of data colleted during a collaboration between AliveCorr, a portable EKG company, and UW Medical Center.

The Control Dataset was X instances of data collected in 2019 from a total of Y patients. Each of the patients has between 1 and 8 distinct EKGs in the dataset. 

The Covid Dataset was 1750 instances of data collected after March of 2020. 130 patients were diagnosed with COVID at the UW Medical Center, and each was sent home with a portable EKG machine. They were asked to self-administer both a COVID viral load test (a nasal swab) and the 30-second EKG using the AliveCorr device for 14 days proceeding their initial COVID positive result. 

Here is an example of that data over 14 days:

![Image](./patient1.png) ![Image](./patient2.png) ![Image](./patient3.png)

The EKG data itself for each patient is a 9000 timestep EKG taken over a 30 second period. The signals look as follows: The peculiar thing about EKGs is that they're similar to a human fingerprint. The 

![Image](./ekg_samples.png)

### Methodology - what is your approach/solution/what did you do?

#### Data Cleaning & Initial Results
This project began with the objective of detecting the exact viral load of a patient (recorded in the COVID-19 dataset as a result of the nasal swab. The viral loads themselves range between () and are only estimates, semiquantitative measures that represents the number of cycles needed to detect the virus. A viral load of 0 indicates no presense of the virus, whereas a viral load of > 0 indicates that the virus is present in the sample. In order to predict viral load from the EKG, I took the X samples of length 9000, normalized them, split them into 5 second chunks (ie 1500 timesteps per instance) and then ran a baseline neural network. The results are as folllows.

Given the timeseries nature of the EKG (a signal) I realized that an LSTM might be able to capture the signal more accurately. Hence I ran a model to predict viral load (a regression output) using the following LSTM. Results are as follows. 

Since the results were poor, for sanity's sake I ran a COVID/not COVID classifier using the COVID-19 dataset. Doing so yeilded a result that was worse that 50%. Given the binary nature of the classification task, this tipped me off that something was inherently wrong with our dataset. 

#### Further Examination of the data:

This happened to be around the time of the Andrej Karpathy lecture, and as per his advice I took a deep look at the dataset and realized that lots of patients would have a high viral load between days 1-4, then the viral load would drop to 0 for a few days, after which the viral load would again rise above 0. This seems to indicate 1 of 3 outcomes
- the patients were not adequately compliant with the guidelines on how to carry out the nasal swab, or 
- the patients were compliant, but the sensitivity of the test was too low to detect the virus on those given days (false negative)
- or finally, that the virus itself ebbs and flows in it's severity over the 14 day period

Given the nature of the data and how I couldn't be certain about the labeling, I decided to reevaluate my approach. Using a seperate corpus of data (described above as the control sample) I created a pretrained model that predicted Age from EKGs. GIven that age is a high fidelity label that we had access to, this method gave the model a good way to learn about EKG data in general. Here are the results:


#### Pretrained Model:
I used the pretrained model and attached a custom head. Results. Tuning.


### Experiments/evaluation - how are you evaluating your results
### Results - How well did you do
### Examples - images/text/live demo, anything to show off your work

### Video - a 2-3 minute long video where you explain your project and the above information

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```
