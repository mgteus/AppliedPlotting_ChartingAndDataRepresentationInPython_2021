
# Assignment 4

Before working on this assignment please read these instructions fully. In the submission area, you will notice that you can click the link to **Preview the Grading** for each step of the assignment. This is the criteria that will be used for peer grading. Please familiarize yourself with the criteria before beginning the assignment.

This assignment requires that you to find **at least** two datasets on the web which are related, and that you visualize these datasets to answer a question with the broad topic of **economic activity or measures** (see below) for the region of **Novo Hamburgo, Rio Grande do Sul, Brazil**, or **Brazil** more broadly.

You can merge these datasets with data from different regions if you like! For instance, you might want to compare **Novo Hamburgo, Rio Grande do Sul, Brazil** to Ann Arbor, USA. In that case at least one source file must be about **Novo Hamburgo, Rio Grande do Sul, Brazil**.

You are welcome to choose datasets at your discretion, but keep in mind **they will be shared with your peers**, so choose appropriate datasets. Sensitive, confidential, illicit, and proprietary materials are not good choices for datasets for this assignment. You are welcome to upload datasets of your own as well, and link to them using a third party repository such as github, bitbucket, pastebin, etc. Please be aware of the Coursera terms of service with respect to intellectual property.

Also, you are welcome to preserve data in its original language, but for the purposes of grading you should provide english translations. You are welcome to provide multiple visuals in different languages if you would like!

As this assignment is for the whole course, you must incorporate principles discussed in the first week, such as having as high data-ink ratio (Tufte) and aligning with Cairo’s principles of truth, beauty, function, and insight.

Here are the assignment instructions:

 * State the region and the domain category that your data sets are about (e.g., **Novo Hamburgo, Rio Grande do Sul, Brazil** and **economic activity or measures**).
 * You must state a question about the domain category and region that you identified as being interesting.
 * You must provide at least two links to available datasets. These could be links to files such as CSV or Excel files, or links to websites which might have data in tabular form, such as Wikipedia pages.
 * You must upload an image which addresses the research question you stated. In addition to addressing the question, this visual should follow Cairo's principles of truthfulness, functionality, beauty, and insightfulness.
 * You must contribute a short (1-2 paragraph) written justification of how your visualization addresses your stated research question.

What do we mean by **economic activity or measures**?  For this category you might look at the inputs or outputs to the given economy, or major changes in the economy compared to other regions.

## Tips
* Wikipedia is an excellent source of data, and I strongly encourage you to explore it for new data sources.
* Many governments run open data initiatives at the city, region, and country levels, and these are wonderful resources for localized data sources.
* Several international agencies, such as the [United Nations](http://data.un.org/), the [World Bank](http://data.worldbank.org/), the [Global Open Data Index](http://index.okfn.org/place/) are other great places to look for data.
* This assignment requires you to convert and clean datafiles. Check out the discussion forums for tips on how to do this from various sources, and share your successes with your fellow students!

## Example
Looking for an example? Here's what our course assistant put together for the **Ann Arbor, MI, USA** area using **sports and athletics** as the topic. [Example Solution File](./readonly/Assignment4_example.pdf)


```python
import pandas as pd
import re
import matplotlib.pyplot as plt
#BraziLData URL: https://www.ibge.gov.br/estatisticas/sociais/trabalho/9173-pesquisa-nacional
#-por-amostra-de-domicilios-continua-trimestral.html?=&t=series-historicas&utm_
#source=landing&utm_medium=explica&utm_campaign=desemprego

#USData URL: https://pkgstore.datahub.io/core/employment-us/aat1_csv/data/d7e5ec6ea0340e846fd84ae6a69519c2/aat1_csv.csv




df_BR = pd.read_csv('BrazilData.csv', sep=';')
df_US = pd.read_csv('UsData.csv')

#df_US = df_US[["year", "unemployed_percent"]]

df_BR.reset_index(drop=False, inplace=True)

df_BR.head()
BrDateTime = []
#listt = df_BR.iloc[0]


for item in df_BR.iloc[0]:
    pass
    #x = item.split()
    #print(x)
    #BrDateTime.append(item)
    
    
    #print(item)
    
#print(BrDateTime)


#k = pd.date_range(start=pd.Timestamp('2012-01-01'), end=pd.Timestamp('2020-01-04'), periods = 10)

k2= pd.date_range(start='1/1/2012', end='31/08/2020', freq='45D')


df_BR.head()
dfbr = pd.DataFrame()
BRvals = []
for item in df_BR.iloc[1]:
    if item == 'Porcentagem':
        pass
    else:
        BRvals.append(item)


dfbr['Porcentagem'] = BRvals
dfbr["Porcentagem"] = dfbr["Porcentagem"].astype('float')


dfbr = dfbr['Porcentagem'][0:71]


dfus = df_US["unemployed_percent"]

```


```python
fig = plt.figure()
ax = fig.add_subplot(111)
aX = fig.gca()

fig.set_size_inches(18.5, 10.5)



plt.plot(k2, dfbr, c='g', label='Brazil',  linewidth=4 )
plt.plot(k2, dfus, c='k', label="United States", linewidth=4 )

plt.legend(loc=8, markerscale=1, fontsize=15)
plt.xlabel("Year", fontsize=20)
plt.ylabel("Percentage (%)", fontsize=25)
plt.title("Unemployed Percentage: \n Brazil vs United States", fontsize=25)




#plt.gca().fill_between(k2, 
                      #dfus, dfbr,  facecolor='red', alpha=0.2)

#aX.fill_between(k2, dfbr, dfus, facecolor='k',where= dfus > dfbr, interpolate= True, alpha=0.4)
aX.fill_between(k2, dfbr, dfus, facecolor='green',where= dfus < dfbr, interpolate= True, alpha=0.2)
#aX.fill_between(k2,dfus, 0, facecolor='k', interpolate= True, alpha=0.4)
#aX.fill_between(k2, dfus, dfbr, facecolor='silver')
ax.tick_params(axis="x", labelsize=20)
ax.tick_params(axis="y", labelsize=30)
ax.grid(axis='y', linestyle='--')

ax.spines['left'].set_linewidth(2)   
ax.spines['bottom'].set_linewidth(2)
ax.spines['right'].set_linewidth(0)
ax.spines['top'].set_linewidth(0)


    
    
#plt.savefig('finalplotA4.png', format='png')
    
    
    
    
    
    
    
    
    
plt.show()


```
