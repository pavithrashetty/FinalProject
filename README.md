##Predicting cab booking cancellations
###Business Context:
* The business problem tackled here is trying to improve customer service for "YourCabs.com", a cab company in Bengaluru, India.
* The problem of interest is booking cancellations by the company due to unavailability of a car.
* The challenge is that cancellations can occur very close to the trip start time, thereby causing passengers inconvenience.

###Goal:
* The goal is to create a predictive model for classifying new bookings as to whether they will eventually get cancelled due to car unavailability.
* This is a classification task that includes misclassification costs.
* The cost of misclassifying an un-cancelled booking as a cancelled booking (cost=1 unit).
* The cost of misclassifying a cancelled booking as un-cancelled booking (penalty of 100 units).

###Given Data:
* <B>Data file name:</B> Kaggle_YourCabs_training.csv
* <B>File descriptions:</B> The training set (over 43,000 bookings). Includes the output Car_Cancellation (0- not cancelled or 1- cancelled) and the misclassification costs in Cost_of_error.
* <B>Data fields:</B>
    * <u>id</u> - booking ID
    * <u>user_id</u> - the ID of the customer (based on mobile number)
    * <u>vehicle_model_id</u> - vehicle model type.
    * <u>package_id </u>- type of package (1=4hrs & 40kms, 2=8hrs & 80kms, 3=6hrs & 60kms, 4= 10hrs & 100kms, 5=5hrs & 50kms, 6=3hrs & 30kms, 7=12hrs & 120kms)
    * <u>travel_type_id</u> - type of travel (1=long distance, 2= point to point, 3= hourly rental).
    * <u>from_area_id</u> - unique identifier of area. Applicable only for point-to-point travel and packages
    * <u>to_area_id </u>- unique identifier of area. Applicable only for point-to-point travel
    * <u>from_city_id</u> - unique identifier of city
    * <u>to_city_id</u> - unique identifier of city (only for intercity)
    * <u>from_date</u> - time stamp of requested trip start
    * <u>to_date </u>- time stamp of trip end
    * <u>online_booking </u>- if booking was done on desktop website
    * <u>mobile_site_booking </u>- if booking was done on mobile website
    * <u>booking_created </u>- time stamp of booking
    * <u>from_lat </u>- latitude of from area, <u>from_long</u> -  longitude of from area, <u>to_lat</u> - latitude of to area, <u>to_long</u> - longitude of to area
    * <u>Car_Cancellation</u> - whether the booking was cancelled (1) or not (0) due to unavailability of a car.
    * <u>Cost_of_error</u> - the cost incurred if the booking is misclassified. For an un-cancelled booking, the cost of misclassificaiton is 1. For a cancelled booking, the cost of misclassificaiton is 100 uniits.

###Features extracted from given data:
* days_rem : It is the difference between time stamp of requested trip start(from_date) and time stamp of booking(booking_created).
* The column from_date is devided into following 3 different columns for convenient:
  * from_month: Extracted the month part from from_date
  * from_weekday: Extracted day of week part from from_date
  * from_time: Extracted time from from_date
* The column booking_created is devided into following 3 different columns for convenient:
  * booking_month: Extracted the month part from booking_created.
  * booking_weekday : Extracted day of week part from booking_created
  * booking_time: Extracted time from booking_created 

###Handling missing values:
* There are many columns in the data set which contains null values (missing data). 
* Here, strategy used to handle missing values is filling all 'NaN' values with number 'zero'(0).

###Technique used (RandomForestClassifier):
* Given data set is divided as “train data set” and “test data set”. 
  * Training set is used to build the model. 
  * Test set is used to test the accuracy of the model after it is being built
* Here, main idea is building multiple models with different sample and different initial variables from train data set.
* Goal is to determine what attributes (or variables) provide the most information that can be used to predict car cancellation.
* We will repeat the process several times and then make a final prediction on each observation. This final prediction can simply be the mean of each prediction.
* This is how our model predicts the best possible outcome from the given set of data.

###AUC is an Error matric used:
* AUC is a metric used to judge predictions in binary response (0/1) problem.
* For a binary choice prediction (car cancellation), there are four possible outcomes:
  * true positive - a positive instance that is correctly classified as positive.
  * false positive - a negative instance that is incorrectly classified as positive.
  * true negative - a negative instance that is correctly classified as negative.
  * false negative - a positive instance that is incorrectly classified as negative.
* The true positive rate, or recall, is calculated as the number of true positives divided by the total number of positives.
* The false positive rate is calculated as the number of false positives divided by the total number of negatives. 
* A perfect model will achieve a true positive rate of 1 and a false positive rate of 0
* ROC is a two-dimensional representation of a model's performance, the AUC distills this information into a single scalar. 
* ROC is created by plotting the "true positive rate" against the "false positive rate".
* AUC as the name implies, it is calculated as the area under the ROC curve.
* A perfect model will score an AUC of 1, while random guessing will score an AUC of around of 0.5. In practice, almost all models will fit somewhere in between.

###ROC curve:
Fig1: ROC curve for our model to predict car cancellation with AUC=0.9

<figure>
  <img src="https://cloud.githubusercontent.com/assets/10646127/7625528/575fa212-f9be-11e4-885a-e313632d14e9.png" alt="ROC curve" width="304" height="228">
  <figcaption>Fig 1. ROC curve</figcaption>
</figure>

###Precision and Recall:
* Precision is the fraction of correctly identified examples of a class (ratio of true positives to all positives).
* Recall is the fraction of observations classified in that class that was correctly classified
* Where high precision relates to a 'low false positive rate', and high recall relates to a 'low false negative rate'.
* <B>For this project:</B>
   * Precision: The model will correctly identify 93% of cab cancellation. Conversely there is a 7% chance it will identify a non-cancelled booking as a cancelled booking. 
   * Recall: out of the car booking the model identified as cancelled, 94% of them will actually be cancelled. That is, our model 6% of the times may identify a cancelled booking as a non-cancelled booking.

###Variable importance measures:
Fig2: Bar chart indicates the importance of the variables in descending order

<figure>
  <img src="https://cloud.githubusercontent.com/assets/10646127/7625669/5c064314-f9c0-11e4-9971-6c80ba79678f.png" alt="ROC curve" width="304" height="228">
  <figcaption>Fig 2. Important variables</figcaption>
</figure>

###Drawbacks of the model:
* Our entire strategy has been to fit a model as strongly as possible to our 20% of the data. 20% sample may not really reflect reality. Our model will over fit to the 20% holdout, and may not actually perform well in real life.
* To overcome over fitting problem we use "Validation" technique. Now, for car cancellation prediction “K-Fold” Cross Validation is used. (Average K-Fold CV score (AUC) is 0.77).
* There is a 7% chance it will identify a non-cancelled booking as a cancelled booking.
* our model 6% of the times may identify a cancelled booking as a non-cancelled booking.

###Cost of error:
* Confusion matrix for the model gives:
   * true positive(tp)=8033
   * false positive(fp)=40
   * false negetive(fn)=488
* Therefore cost of error is given by:
   * fnx100=>488x100=48800 units (cost of miss-classification as non-cancelled instead cancelled)
   * fpx1=>40x1=40 units (cost of miss-classification as cancelled instead non-cancelled)

####source:
* https://inclass.kaggle.com/c/predicting-cab-booking-cancellations
* https://www.kaggle.com/wiki/AUC




