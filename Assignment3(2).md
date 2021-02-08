
# Assignment 3 - Building a Custom Visualization

---

In this assignment you must choose one of the options presented below and submit a visual as well as your source code for peer grading. The details of how you solve the assignment are up to you, although your assignment must use matplotlib so that your peers can evaluate your work. The options differ in challenge level, but there are no grades associated with the challenge level you chose. However, your peers will be asked to ensure you at least met a minimum quality for a given technique in order to pass. Implement the technique fully (or exceed it!) and you should be able to earn full grades for the assignment.


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ferreira, N., Fisher, D., & Konig, A. C. (2014, April). [Sample-oriented task-driven visualizations: allowing users to make better, more confident decisions.](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/Ferreira_Fisher_Sample_Oriented_Tasks.pdf) 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;In Proceedings of the SIGCHI Conference on Human Factors in Computing Systems (pp. 571-580). ACM. ([video](https://www.youtube.com/watch?v=BI7GAs-va-Q))


In this [paper](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/Ferreira_Fisher_Sample_Oriented_Tasks.pdf) the authors describe the challenges users face when trying to make judgements about probabilistic data generated through samples. As an example, they look at a bar chart of four years of data (replicated below in Figure 1). Each year has a y-axis value, which is derived from a sample of a larger dataset. For instance, the first value might be the number votes in a given district or riding for 1992, with the average being around 33,000. On top of this is plotted the 95% confidence interval for the mean (see the boxplot lectures for more information, and the yerr parameter of barcharts).

<br>
<img src="readonly/Assignment3Fig1.png" alt="Figure 1" style="width: 400px;"/>
<h4 style="text-align: center;" markdown="1">  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Figure 1 from (Ferreira et al, 2014).</h4>

<br>

A challenge that users face is that, for a given y-axis value (e.g. 42,000), it is difficult to know which x-axis values are most likely to be representative, because the confidence levels overlap and their distributions are different (the lengths of the confidence interval bars are unequal). One of the solutions the authors propose for this problem (Figure 2c) is to allow users to indicate the y-axis value of interest (e.g. 42,000) and then draw a horizontal line and color bars based on this value. So bars might be colored red if they are definitely above this value (given the confidence interval), blue if they are definitely below this value, or white if they contain this value.


<br>
<img src="readonly/Assignment3Fig2c.png" alt="Figure 1" style="width: 400px;"/>
<h4 style="text-align: center;" markdown="1">  Figure 2c from (Ferreira et al. 2014). Note that the colorbar legend at the bottom as well as the arrows are not required in the assignment descriptions below.</h4>

<br>
<br>

**Easiest option:** Implement the bar coloring as described above - a color scale with only three colors, (e.g. blue, white, and red). Assume the user provides the y axis value of interest as a parameter or variable.


**Harder option:** Implement the bar coloring as described in the paper, where the color of the bar is actually based on the amount of data covered (e.g. a gradient ranging from dark blue for the distribution being certainly below this y-axis, to white if the value is certainly contained, to dark red if the value is certainly not contained as the distribution is above the axis).

**Even Harder option:** Add interactivity to the above, which allows the user to click on the y axis to set the value of interest. The bar colors should change with respect to what value the user has selected.

**Hardest option:** Allow the user to interactively set a range of y values they are interested in, and recolor based on this (e.g. a y-axis band, see the paper for more details).

---

*Note: The data given for this assignment is not the same as the data used in the article and as a result the visualizations may look a little different.*


```python
# Use the following data for this assignment:

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.cm as cm
from matplotlib.colors import Normalize
from matplotlib.cm import get_cmap

np.random.seed(12345)

df = pd.DataFrame([np.random.normal(32000,200000,3650), 
                   np.random.normal(43000,100000,3650), 
                   np.random.normal(43500,140000,3650), 
                   np.random.normal(48000,70000,3650)], 
                  index=[1992,1993,1994,1995])
#df.head()


```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>3640</th>
      <th>3641</th>
      <th>3642</th>
      <th>3643</th>
      <th>3644</th>
      <th>3645</th>
      <th>3646</th>
      <th>3647</th>
      <th>3648</th>
      <th>3649</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1992</th>
      <td>-8941.531897</td>
      <td>127788.667612</td>
      <td>-71887.743011</td>
      <td>-79146.060869</td>
      <td>425156.114501</td>
      <td>310681.166595</td>
      <td>50581.575349</td>
      <td>88349.230566</td>
      <td>185804.513522</td>
      <td>281286.947277</td>
      <td>...</td>
      <td>171938.760289</td>
      <td>150650.759924</td>
      <td>203663.976475</td>
      <td>-377877.158072</td>
      <td>-197214.093861</td>
      <td>24185.008589</td>
      <td>-56826.729535</td>
      <td>-67319.766489</td>
      <td>113377.299342</td>
      <td>-4494.878538</td>
    </tr>
    <tr>
      <th>1993</th>
      <td>-51896.094813</td>
      <td>198350.518755</td>
      <td>-123518.252821</td>
      <td>-129916.759685</td>
      <td>216119.147314</td>
      <td>49845.883728</td>
      <td>149135.648505</td>
      <td>62807.672113</td>
      <td>23365.577348</td>
      <td>-109686.264981</td>
      <td>...</td>
      <td>-44566.520071</td>
      <td>101032.122475</td>
      <td>117648.199945</td>
      <td>160475.622607</td>
      <td>-13759.888342</td>
      <td>-37333.493572</td>
      <td>103019.841174</td>
      <td>179746.127403</td>
      <td>13455.493990</td>
      <td>34442.898855</td>
    </tr>
    <tr>
      <th>1994</th>
      <td>152336.932066</td>
      <td>192947.128056</td>
      <td>389950.263156</td>
      <td>-93006.152024</td>
      <td>100818.575896</td>
      <td>5529.230706</td>
      <td>-32989.370488</td>
      <td>223942.967178</td>
      <td>-66721.580898</td>
      <td>47826.269111</td>
      <td>...</td>
      <td>165085.806360</td>
      <td>74735.174090</td>
      <td>107329.726875</td>
      <td>199250.734156</td>
      <td>-36792.202754</td>
      <td>-71861.846997</td>
      <td>26375.113219</td>
      <td>-29328.078384</td>
      <td>65858.761714</td>
      <td>-91542.001049</td>
    </tr>
    <tr>
      <th>1995</th>
      <td>-69708.439062</td>
      <td>-13289.977022</td>
      <td>-30178.390991</td>
      <td>55052.181256</td>
      <td>152883.621657</td>
      <td>12930.835194</td>
      <td>63700.461932</td>
      <td>64148.489835</td>
      <td>-29316.268556</td>
      <td>59645.677367</td>
      <td>...</td>
      <td>-13901.388118</td>
      <td>50173.686673</td>
      <td>53965.990717</td>
      <td>4128.990173</td>
      <td>72202.595138</td>
      <td>39937.199964</td>
      <td>139472.114293</td>
      <td>59386.186379</td>
      <td>73362.229590</td>
      <td>28705.082908</td>
    </tr>
  </tbody>
</table>
<p>4 rows × 3650 columns</p>
</div>




```python
get_ipython().magic('matplotlib notebook')
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.cm as cm
from matplotlib.colors import Normalize
from matplotlib.cm import get_cmap


dfMean = df.mean(axis=1)
dfStd = df.std(axis=1)/np.sqrt(df.shape[1])



Y = 39000     # y value
p = 1.96      # 2 stds variation


Norm = Normalize(vmin=-1.96, vmax= 1.96) # setting the normalize function params

colormap = get_cmap('PRGn')          # selecting the colormap pallete
# Choose anyone in 'Diverging Colormaps' section at: https://matplotlib.org/3.3.3/tutorials/colors/colormaps.html


dfColor = pd.DataFrame()                # creating a dataframe for the color-value realtion
dfColor["Value"] = Norm((dfMean-Y)/dfStd)
dfColor["Color"] = [colormap(x) for x in dfColor["Value"]]   # getting the color by the normalized value


#------------ PLOT CONFIGS -------------
bar_plot = plt.bar(df.index, dfMean, yerr=dfStd*p, color = dfColor["Color"])


h_line = plt.axhline(y=Y, c='k', linewidth=2, linestyle='--')
plt.xticks(df.index, ('1992','1993','1994','1995'))
text_box = plt.text(1995.45, y, 'Y = %d' %Y, bbox=dict(fc='white',ec='k'))




def onclick(event):
    for i in range(4):
        shade = colormap(Norm((dfMean.values[i]-event.ydata)/dfStd.values[i]))
        bar_plot[i].set_color(shade) 
    h_line.set_ydata(event.ydata)
    #text_box
    text_box.set_text('y = %d' %event.ydata)
    text_box.set_position((1995.45, event.ydata+350))
    
plt.gcf().canvas.mpl_connect('button_press_event', onclick)
plt.savefig('\A3graph.png', format='png')



```


    <IPython.core.display.Javascript object>



<img src="data:image/png;base64,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kbk3eb/pN+IseL/pt9PxRmbeyJx9+/8W0dMhomcjED29yTsQIs6CdyAQPZ1zj4Ro20D0fPzjH0+f//znB/3aT37ykzRhwoT08MMPZznPJUuWpI9+9KNpiy22SJMnT0777bffkFdATj/99LTDDjukiRMnpk996lPp+eefH/RnXHzxxWn77bdP48ePT4cffnh68cUXB10/66yz0tZbb50mTZqUjjvuuPTaa681rr355pvpc5/7XJoyZUqaOnVqOu2009I777wz7GMlejYC0dObvAMh4ix4BwLR0zn3SIi2DUTPihUr0pQpU9Jtt92WUqoCZM899xz03sdN9atf/So98cQT6de//nV655130rXXXpsmTZqUXn/99ZRSSn//93+f9tprr/Tss8+mdevWpc9+9rNpxowZjd9/yy23pKlTp6bFixenNWvWpJkzZ6bPfOYzjeuXXXZZmjZtWnryySfTiy++mPbff/908sknN66fddZZaZ999knPP/98evrpp9Muu+ySvvnNbw77WImejUD09CbvQIg4C96BQPR0zj0Sou1dXt66/PLL07Rp09Irr7ySzj777LTvvvumt99+2+Rsf/3rX6frr78+SUrLly9PKaW0zz77pCuvvLJxz0B4DLxPc9asWemkk05qXF+yZEnabLPN0urVq1NKKR1wwAHpggsuaFz/0Y9+lKZOndp4NmeHHXZIN9xwQ+P6v/3bv6U99thj2MdH9GwEoqc3eQdCxFnwDgSip3PukRBtHbyn55BDDkmHHnpomjx58qAPmTSbO3dumjx5ctvNnTt3g+e64447ppEjq0/JNX96d++9905XXHFF45+XLl2aJDVCZffddx90PaWUxo0blxYsWJBSSmnSpEnp9ttvb1xbtWpVkpSeeeaZ9NJLLw353/2+++5Lo0aNGvYlLqJnIxA9vck7ECLOgncgED2dc4+EaOsgelauXJnGjx+f9WWt4bz22mvp6quvTv/+7//e+LXZs2enPfbYIy1fvjytXbs2zZo1K/X19aVvfetbKaWU3ve+96Xrrrtu0J+z3XbbpR/+8IcppZRGjBgx6FPIb775ZpKUHn300bR8+fIkKb388suN6wM/zqL5fT8DiJ6NQPT0Ju9AiDgL3oFA9HTOPRKircNPb+20007pxhtvNDvXZrvuumu65557UkrVj2g55ZRT0o477pi23Xbb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" width="639.85">

