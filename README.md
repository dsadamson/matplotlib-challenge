Visualizing and Analyzing Mouse Treatment Data

#Overview

In this assignment, I was asked to visualize and analyze the treatment data of 249 different mice in 10 different treatment groups for SCC tumors. At the end of my assignment, I was asked to analyze the performance of the treatment Capomulin, based on the visualizations I produced. This analysis is featured at the top of the file entitled Pymaceuticals.ipynb. 

To accomplish this task, I made extensive use of Pandas and Matplotlib. Both the starter code, Pymaceutical_starter.ipynb, and the .csv files used to produce this code are included in the repository.


#Preparing the Data

With this assignment, I received two .csv files entitled Study_results.csv and Mouse_metadata.csv. To analyze the progress of these mice, their tumors, and their treatment, I merged these .csv files into a single DataFrame.
Next, I had to clean the data of any duplicated mouse data to prevent it from being skewed. This was done by telling the code to search for and store any Mouse IDs that had duplicated measurements in their timepoints. Then, using boolean indexing, I displayed all Mouse IDs with duplicated measurements, found the duplicated ID, and dropped it from the data. This left me with a sample of 248 mice.

#Summary Statistics

I used the 'groupby()' method to find the measures of central tendency and standard error for the tumor volumes in each treatment group, then used a dictionary to sort each treatment's summary statistics into a DataFrame.
In the cell below, I did the same, though this time with the aggregation method.


#Bar and Pie Charts

In the next section, I compared test groups by their number of timepoints using bar plots and the total number of male mice to female mice in this study.
To find and sort the timepoints for the bar plot section, I used the 'groupby()' and 'count()' methods like so:

  timepoints = cleaned_data.groupby("Drug Regimen")["Timepoint"].count()
  
I created two identical bar plots, one with Pandas and the other with Matplotlib. 

Using a 'value_count()' method, I found all the male and female mice recorded in the DataFrame column marked "Sex." Then, using Pandas and Matplotlib, I created two identical pie plots.


# Quartiles, Outliers and Boxplots

In the next section, I relied heavily on finding the final timepoints and tumor volumes of each mouse. First, I had to include this data in a DataFrame. To find, this data I used a 'sort_values()' method to find the final time point and tumor volume of each Mouse ID, then to construct them into a DataFrame like so:

  final_tumor_vol = cleaned_data.sort_values("Timepoint", ascending=False).groupby("Mouse ID").first()["Tumor Volume (mm3)"]
final_tumor_vol = pd.DataFrame(final_tumor_vol)

Then, I found the value of the max time point, using the following line of code:

  max_timepoints = cleaned_data.groupby("Mouse ID")["Timepoint"].max().to_frame().reset_index()

Finally, I merged these DataFrames together, then merged them into the cleaned DataFrame I created when I prepared the data.

To find the outliers of each treatment group, I had to write an extensive block of code, including two 'for' loops. First, I had the code track the final timepoint for each Mouse ID, then I had it add all unique treatments to a list. Then, I created an empty list to record final tumor volumes.
After creating this empty list, I started my first 'for' loop, telling the code to track tumor volumes by 'treatment', as found in the DataFrame column 'Drug Regimen.' The code would then look to the "Tumor Volume (mm3)" column and add values to the empty tumor volume list, sorted by treatment group.
After resetting the DataFrame index, the code used another 'for' loop to determine IQR and outliers for each treatment group, which is displayed below: 

  # create a loop to find upper bounds, lower bounds, and outliers in each treatment column
for treatment in treatments:
    # subset the final tumor volume DataFrame for the current treatment
    treatment_df = final_tumor_volume_df[final_tumor_volume_df["Drug Regimen"] == treatment]
    # extract the tumor volume data for the current treatment
    tumor_volumes = treatment_df["Tumor Volume (mm3)"]
    # calculate the IQR, Q1 and Q3 for the tumor volume data
    Q1 = tumor_volumes.quantile(0.25)
    Q3 = tumor_volumes.quantile(0.75)
    IQR = Q3 - Q1
    # find upper and lower bounds
    upper_bound = Q3 + 1.5 * IQR
    lower_bound = Q1 - 1.5 * IQR
    # find potential outliers
    outliers = tumor_volumes[(tumor_volumes <= lower_bound) | (tumor_volumes >= upper_bound)]
  
 To show the distribution of tumor volumes for each treatment group, I used Pandas to create a box-and-whisker plot.
  
  
# Line and Scatter Plots
In part of this assignment, I was asked to track the tumor size of a single Mouse ID over its recorded time points. I decided to focus on a mouse in the Capomulin group because this assignment asked me to focus on that treatment, then I found the median final tumor size of mice in the Capomulin group. I selected Mouse j246 because its final tumor size was closest to that median point. I then used Pandas to create the line plot.

To create the scatter plots, I set the selected treatment as Capomulin as I was assigned to do, then I subsetted the merged data frame to track all data from that selected treatment group and created empty lists to track each mouse's average weight and tumor size. Using a 'for' loop, I again subetted the data to track specific Mouse IDs. Using a 'mean()' function, I found each mouse's average weight and tumor size, both of which were stored to the empty lists I created before the loop. Using Matplotlib, these lists were made into a scatter plot, with average weights acting as the x-axis values and tumor sizes acting as the y-axis values. 
To find the correlation coefficient and linear regression model, I used the 'st.pearsonr()' and 'st.lineregress()' methods respectively, and printed a data frame with the linear regression and the correlation coefficient below the Jupyter Notebook cell.
 
