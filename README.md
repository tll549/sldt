
This is a simple script that has only two functions for one purpose. It allows you to save files with date time appended behind the filename but in front of the extension, like `sldt.s(variable, 'filename.csv')` will save *variable* to a file "filename_1912181212.csv". Everytime you want to load it back, use `sldt.l('filename.csv')`, it will automatically find the newest file.

This is useful when you want to save some intermediate result during running your code. But be aware the saved files will keep growing everytime you run.

Feel free to download/edit this script, and maybe share your version with me!

# Installation

```
pip install sldt
```

# Supported File Types

It supports only 4 common file types now showed below. Basically it just calls like `pd.DataFrame.to_csv()` as you already familiar.


```python
import sldt
```


```python
sldt.__version__
```




    '1.0.2'




```python
sldt.SUPPORTED_EXT
```




    ['.pkl', '.csv', '.png', '.txt']



# Demo


```python
# launch logger to see info when saving and loading files
import logging
logging.getLogger().setLevel(logging.INFO)
```

## anything -> pkl


```python
a_list = ['a', 0.1, False]
# save
sldt.s(a_list, 'output/a_list.pkl')

# save the second file for demo
import time
time.sleep(60)
a_list = ['b', 0.2, True]
sldt.s(a_list, 'output/a_list.pkl')

# load the newest (which is the later one) back
a_list = sldt.l('output/a_list.pkl')
a_list
```

    INFO:root:output/a_list_1912240334.pkl saved
    INFO:root:output/a_list_1912240335.pkl saved
    INFO:root:output/a_list_1912240335.pkl loaded





    ['b', 0.2, True]



## pandas dataframe -> csv


```python
import pandas as pd
df = pd.DataFrame({'col1': [1, 2], 'col2': [3, 4]})
sldt.s(df, 'output/df.csv')

df = sldt.l('output/df.csv')
df
```

    INFO:root:output/df_1912240335.csv saved
    INFO:root:output/df_1912240335.csv loaded

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
you can pass any argument as to `pd.DataFrame.to_csv()`, if there's no arguments in `save_dt`, the default will be `index=False`.

For example, `save_dt(df, 'output/df.csv', sep=';')`

## figure -> png


```python
import seaborn as sns
exercise = sns.load_dataset("exercise")
g = sns.catplot(x="time", y="pulse", hue="kind", data=exercise)

sldt.s(g, 'output/g.png')
```

    INFO:root:output/g_1912240335.png saved


Display it using `![](output/g_1912240037.png)` in markdown.

the default arguments will be `dpi=600, bbox_inches='tight'`. And it will try to close the fig after saving the file.

## string -> txt


```python
text = '''some random sentences
here
and there'''
sldt.s(text, 'output/text.txt')

text = sldt.l('output/text.txt')
text
```

    INFO:root:output/text_1912240335.txt saved
    INFO:root:output/text_1912240335.txt loaded





    'some random sentences\nhere\nand there'



# helper functions

you can save your own file type and load it back using `append_dt` and `find_newest`


```python
import pickle

output_filename = sldt.append_dt('output/sth.pkl', datetime_format="%y%m%d%H%M")[0]
with open(output_filename, 'wb') as f:
    pickle.dump('some strings', f)
```


```python
newest_file = sldt.find_newest('output/sth.pkl')[0]
with open(newest_file, 'rb') as f:
    sth = pickle.load(f)
sth
```




    'some strings'



`find_newest` returns a tuplet (filename, extension)


```python
sldt.find_newest('output/text.txt')
```




    ('output/text_1912240335.txt', '.txt')



It can be served as checking whether file is exist


```python
try:
    sldt.find_newest('output/text.txt')
    print('file exist')
except:
    print('no file exist')
```

    file exist


Similarly, load only if you haven't save the needed file


```python
try:
    sldt.l('output/result.csv')
except:
    # do some calculation
    result = pd.DataFrame({'col1': [1, 2], 'col2': [3, 4]})
    # save calculation result
    sldt.s(result, 'output/result.csv')
```

    INFO:root:output/result_1912240335.csv saved

