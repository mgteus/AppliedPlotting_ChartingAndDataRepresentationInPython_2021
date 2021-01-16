
# Assignment 2

Before working on this assignment please read these instructions fully. In the submission area, you will notice that you can click the link to **Preview the Grading** for each step of the assignment. This is the criteria that will be used for peer grading. Please familiarize yourself with the criteria before beginning the assignment.

An NOAA dataset has been stored in the file `data/C2A2_data/BinnedCsvs_d400/fb441e62df2d58994928907a91895ec62c2c42e6cd075c2700843b89.csv`. This is the dataset to use for this assignment. Note: The data for this assignment comes from a subset of The National Centers for Environmental Information (NCEI) [Daily Global Historical Climatology Network](https://www1.ncdc.noaa.gov/pub/data/ghcn/daily/readme.txt) (GHCN-Daily). The GHCN-Daily is comprised of daily climate records from thousands of land surface stations across the globe.

Each row in the assignment datafile corresponds to a single observation.

The following variables are provided to you:

* **id** : station identification code
* **date** : date in YYYY-MM-DD format (e.g. 2012-01-24 = January 24, 2012)
* **element** : indicator of element type
    * TMAX : Maximum temperature (tenths of degrees C)
    * TMIN : Minimum temperature (tenths of degrees C)
* **value** : data value for element (tenths of degrees C)

For this assignment, you must:

1. Read the documentation and familiarize yourself with the dataset, then write some python code which returns a line graph of the record high and record low temperatures by day of the year over the period 2005-2014. The area between the record high and record low temperatures for each day should be shaded.
2. Overlay a scatter of the 2015 data for any points (highs and lows) for which the ten year record (2005-2014) record high or record low was broken in 2015.
3. Watch out for leap days (i.e. February 29th), it is reasonable to remove these points from the dataset for the purpose of this visualization.
4. Make the visual nice! Leverage principles from the first module in this course when developing your solution. Consider issues such as legends, labels, and chart junk.

The data you have been given is near **Ann Arbor, Michigan, United States**, and the stations the data comes from are shown on the map below.


```python
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import mplleaflet
import numpy as np 
import pandas as pd
import re
import warnings                   # use this to hide the warning messages 
warnings.filterwarnings('ignore') # 


df = pd.read_csv("data/C2A2_data/BinnedCsvs_d400/fb441e62df2d58994928907a91895ec62c2c42e6cd075c2700843b89.csv")

df.sort_values('Date', inplace=True)
  

#------ DROPPING '02-29' DAYS ---------------------
df = df[~df.Date.str.contains('(=?\d{4}\-)(02\-29)', regex=True, na=False)]
#pattern1 = '(=?\d{4}\-)(02\-29)'



#------- SPLITING THE DATAFRAMES -------------------
#pattern2 = (2015\-\d*\-\d*)
df1 = df[~df.Date.str.contains('(2015\-\d*\-\d*)', regex=True, na=False)] # spliting using regex 
df2015 = df.loc[df["Date"]>"2015"]                                        # spliting using .loc[]


#print(df.shape[0] - (df1.shape[0] + df2015.shape[0])) #this should print a zero if the split is correct

#---------- SLECTING ONLY MAX AND MIN FOR EACH DAY IN THE FIRST TEN YEARS -----------
df1_max = df1.groupby('Date').max()
df1_max.reset_index(drop=False, inplace=True)  # reseting index to further usage
df1_min = df1.groupby('Date').min()
df1_min.reset_index(drop=False, inplace=True)  # reseting index to further usage


#---------- SLECTING ONLY MAX AND MIN FOR EACH DAY IN THE LAST YEAR -----------
df2015_max = df2015.groupby('Date').max()
df2015_min = df2015.groupby('Date').min()




#----------- FIRST DECADE MAX VALUES SETUP -----------------
month = []        # lists for each month, 
day = []          # day and 
year = []         # year

for item in df1_max["Date"]:  # for loop spliting the date values in "Date" column
    x = item.split('-')
    month.append(x[1])
    day.append(x[-1])
    year.append(x[0])
    
df1_max["Y"] = year           # setting up the list as columns 
df1_max["M"] = month          # in the first decade max values
df1_max["D"] = day            # dataframe 

df1_max = df1_max[[ "Y", "M", "D","Data_Value"]]
df1_max.set_index(["Y", "M", "D"], inplace=True)
df1_max = df1_max.unstack(["M", "D"])


decade_max = df1_max.max(axis=0)            # this method returns a Series


decade_max = decade_max.to_frame(name="T")  # using 'to_frame' to transform decade_max series to a dataframe
decade_max.reset_index(drop=False)




#----------- FIRST DECADE MIN VALUES SETUP -----------------
month2 = []        # lists for each month, 
day2 = []          # day and 
year2 = []         # year

for item in df1_min["Date"]:  # for loop spliting the date values in "Date" column
    x = item.split('-')
    month2.append(x[1])
    day2.append(x[-1])
    year2.append(x[0])
    
df1_min["Y"] = year2           # setting up the list as columns 
df1_min["M"] = month2          # in the first decade max values
df1_min["D"] = day2           # dataframe 

df1_min = df1_min[[ "Y", "M", "D","Data_Value"]]
df1_min.set_index(["Y", "M", "D"], inplace=True)
df1_min = df1_min.unstack(["M", "D"])


decade_min = df1_min.min(axis=0)            # this method returns a Series


decade_min = decade_min.to_frame(name="T")  # using 'to_frame' to transform decade_max series to a dataframe
decade_min.reset_index(drop=False)




#------------------ 2015 RECORD LOW POINTS -----------------------
df2015_min["T"] = df2015_min["Data_Value"]
df2015_min = df2015_min[["T"]]


mask_min=[]
for i in range(365):
    x = df2015_min.iloc[i] < decade_min.iloc[i]
    mask_min.append(x["T"])

min_2015 = df2015_min.copy()
min_2015 = min_2015[mask_min]

min_2015.reset_index(drop=False, inplace=True)
min_2015["Date"] = pd.to_datetime(min_2015["Date"])


#------------------ 2015 RECORD HIGH POINTS -----------------------
df2015_max["T"] = df2015_max["Data_Value"]
df2015_max = df2015_max[["T"]]


mask_max=[]
for i in range(365):
    x = df2015_max.iloc[i] > decade_max.iloc[i]
    mask_max.append(x["T"])

max_2015 = df2015_max.copy()
max_2015 = max_2015[mask_max]

max_2015.reset_index(drop=False, inplace=True)
max_2015["Date"] = pd.to_datetime(max_2015["Date"])




#------------ PLOT CONFIGURATIONS --------------------------------

days = pd.date_range(start="2015-01-01",end="2015-12-31").tolist() # list to easy use in this datetime plot

fig = plt.figure()
ax = fig.add_subplot(111)
fig.set_size_inches(18.5, 10.5)


#-------- DATA POINTS -------------------

plt.plot(days, decade_max["T"]/10)                                 # first decade max values
plt.plot(days, decade_min["T"]/10)                                 # first decade min values
plt.scatter(list(max_2015["Date"]), list(max_2015["T"]/10))        # 2015 record high days
plt.scatter(list(min_2015["Date"]), list(min_2015["T"]/10))        # 2015 record low days


plt.gca().fill_between(days, 
                       decade_min["T"]/10, decade_max["T"]/10)

ax.set(xlabel="Month",
       ylabel="Temperature (ºC)",
       title="Temperature Comparsion:\n 2005-2014 vs 2015",
       xlim=["2014-12-31", "2016-01-01"])
# Define the date format
ax.xaxis.set_major_formatter(mdates.DateFormatter('%m'))
plt.show()





```


```python
#------------ PLOT CONFIGURATIONS --------------------------------

days = pd.date_range(start="2015-01-01",end="2015-12-31").tolist() # list to easy use in this datetime plot

fig = plt.figure()
ax = fig.add_subplot(111)
fig.set_size_inches(18.5, 10.5)                                    # figures size


#--------------- CHART CONFIGS --------------------------------



ax.xaxis.set_major_formatter(mdates.DateFormatter('%b'))            # x-axis date format, '%b' stands for
                                                                    # 'jan, feb, ...' and '%m' -> '01, 02,...'
ax.xaxis.set_major_locator(mdates.MonthLocator(interval=1))         # x-axis ticks interval 




plt.legend(loc=8, markerscale=1, fontsize=15)                         # legend block configs
plt.xlabel("Month", fontsize=20)                                      # axis
plt.ylabel("Temperature ($\circ C$)", fontsize=20)                    # and
plt.title("Temperature Comparsion:\n 2005-2014 vs 2015", fontsize=25) # title
plt.xlim("2014-12-31", "2015-12-31")                                  # configs



ax.tick_params(axis="x", labelsize=20)                           # increasing the axis width and fontsize
ax.tick_params(axis="y", labelsize=15)
for axis in ['top','bottom','left','right']:                     # for loop increasing the four axis lines width 
    ax.spines[axis].set_linewidth(1.5)
    
  


#-------- DATA POINTS -------------------

plt.plot(days, decade_max["T"]/10 , c='k', alpha=0.8,
         label="Decade Highs and Lows", linestyle='--')                         # first decade max values
plt.plot(days, decade_min["T"]/10 , c='k', alpha=0.8,
          label='_nolegend_', linestyle='--')                         # first decade min values
plt.scatter(list(max_2015["Date"]), list(max_2015["T"]/10),
            label="2015' Record High Points", s=80, marker="^", c='r')        # 2015 record high days
plt.scatter(list(min_2015["Date"]), list(min_2015["T"]/10),
            label="2015' Record Low Points", s=80, marker='v', c='navy')        # 2015 record low days

plt.gca().fill_between(days, 
                       decade_min["T"]/10, decade_max["T"]/10, 
                       facecolor='silver', 
                       alpha=0.2)


  
    

#plt.savefig('finalplotA2.png', format='png')                    # saving the .png file 
    
plt.show()

```


# Final Plot Image:

![alt text](https://github.com/mgteus/AppliedPlotting_ChartingAndDataRepresentationInPython/blob/main/image.jpg?raw=true)
