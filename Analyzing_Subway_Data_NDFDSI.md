Subway Data Analysis[¶](#Subway-Data-Analysis) {#Subway-Data-Analysis}
==============================================

Introduction[¶](#Introduction) {#Introduction}
------------------------------

The NYC public transportantion system - Metro Transit Authority -
provides data for download via csv files. Part of the information
available are data from the subway turnstiles, containing weekly logs
for cumulative entries and exits by turnstile and by subway station
during a provided timeframe.

For this project, we will only use the information available at:
[http://web.mta.info/developers/turnstile.html](http://web.mta.info/developers/turnstile.html).

About this project[¶](#About-this-project) {#About-this-project}
==========================================

For this project, you will apply the knowledge acquired in the first
month of this course. We will practice basic data acquisition and data
cleaning tasks to find out fundamental stuff about the data using what
we learned in the Statistics course.

The goal of this project is to explore the relationship between data
from the NYC Subway turnstiles and the city weather. For this, besides
data from the subway, we will also need data from the weather in NYC.

Here are the main points that will be considered in this work:

-   Gathering data from the Internet
-   Using Statistics for Data Analysis
-   Data handling and simple graphics creation with `Pandas`

*How to find help*: We suggest that you try the following channels, in
the following order:

  Type of Question\\Channels      Google   Forum   Slack   Email
  ------------------------------- -------- ------- ------- -------
  Pandas and Python Programming   1        2       3       
  Projects Requiriments                    1       2       3
  Projects Specific Parts                  1       2       3

Here is the address for each of these channels:

-   Forum:
    [https://discussions.udacity.com/c/ndfdsi-project](https://discussions.udacity.com/c/ndfdsi-project)
-   Slack: [Big Data Foundations](https://goo.gl/4K7LWK)
-   Email: india@udacity.com

**The student is expected to submit this report including:**

-   All TODO's completed, as they are crucial for the code to run
    accordingly
-   The ipynb file, exported as html

To submit this project, go to the
[classroom](https://coco.udacity.com/nanodegrees/nd100-inbig/locale/en-us/versions/1.0.0/parts/469348/modules/469702/lessons/469703/project),
and submit your zipped `.ipynb` and html.

Reminders[¶](#Reminders) {#Reminders}
========================

Before we start, there are a few things you must have in mind while
using iPython notebooks:

-   Remember you can see, in the left side of a code cell, when was the
    last time it ran, if there is a number inside the keys.
-   When starting a new session in the notebook, please make sure to run
    all cells up to the point where you last left it. Even if the output
    can still be viewed from the moment you ran your cells in the
    previews session, the kernel starts in a new state, so you will need
    to reload all data, etc. in a new session.
-   The previous point is useful to have in mind if your answers do not
    match what is expected from the quizzes in the classroom. Try
    reloading the data and running all processing steps, one by one, to
    make sure you're working with the same variables and data from each
    step of the quizz.

Session 1 - Data Gathering[¶](#Session-1---Data-Gathering) {#Session-1---Data-Gathering}
----------------------------------------------------------

### *Exercise 1.1*[¶](#Exercise-1.1) {#Exercise-1.1}

Let's do it!! Now it's your turn to gather data. Please write bellow a
Python code to access the link
[http://web.mta.info/developers/turnstile.html](http://web.mta.info/developers/turnstile.html)
and download all files from June 2017. The file must be named
turnstile\_100617.txt, where 10/06/17 is the file's date.

Please see below a few commands that might help you:

Use the **urllib** library to open and redeem a webpage. Use the command
below, where **url** is the webpage path to the following file:

    u = urllib.urlopen(url)
    html = u.read()

Use the **BeautifulSoup** library to search for the link to the file you
want to donwload in the page. Use the command below to create your
*soup* object and search for all 'a' tags in the document:

    soup = BeautifulSoup(html, "html.parser")
    links = soup.find_all('a')

A tip to only download the files from June is to check data in the name
of the file. For instance, to donwload the 17/06/2017 file, please see
if the link ends with *"turnstile\_170610.txt"*. If you forget to do
this, you will download all files from that page. In order to do this,
you can use the following command:

    if '1706' in link.get('href'):

Our final tip is to use the command bellow to download the txt file:

    urllib.urlretrieve(link_do_arquivo, filename)

Please remember - you first have to load all packages and functions that
will be used in your analysys.

In [1]:

    import urllib
    import requests
    from bs4 import BeautifulSoup

    url="http://web.mta.info/developers/"
    urlLink = requests.get(url + "turnstile.html")
    html = urlLink.text
    soup = BeautifulSoup(html,"html.parser")
    links = soup.find_all('a')
    for link in links:
        u = str(link.get('href'))
        if '1706' in u:
            getFileName = link.get('href').split('/')[-1].split('_')
            print(u)
            date = getFileName[1][0:2]
            month = getFileName[1][2:4]
            year = getFileName[1][4:6]
            string = getFileName[0] + "_" + date + month + year + ".txt"
            print(string)
            urllib.request.urlretrieve(url + '/' + u, string)

    data/nyct/turnstile/turnstile_170624.txt
    turnstile_170624.txt
    data/nyct/turnstile/turnstile_170617.txt
    turnstile_170617.txt
    data/nyct/turnstile/turnstile_170610.txt
    turnstile_170610.txt
    data/nyct/turnstile/turnstile_170603.txt
    turnstile_170603.txt

### *Exercise 1.2*[¶](#Exercise-1.2) {#Exercise-1.2}

Write down a function that takes the list of all names of the files you
downloaded in Exercise 1.1 and compile them into one single file. There
must be only one header line in the output file.

For example, if file\_1 has: line 1... line 2...

and the other file, file\_2, has: line 3... line 4... line 5...

We must combine file\_1 and file\_2 into one master file, as follows:

'C/A, UNIT, SCP, DATEn, TIMEn, DESCn, ENTRIESn, EXITSn' line 1... line
2... line 3... line 4... line 5...

In [2]:

    def create_master_turnstile_file(filenames, output_file):
        with open(output_file, 'w') as master_file:
            master_file.write('C/A,UNIT,SCP,STATION, LINENAME, DIVISION, DATEn,TIMEn,DESCn,ENTRIESn,EXITSn\n')
            for filename in filenames:
                file1 = open(filename,'r')
                count = 0
                for row in file1:
                    if(count == 1):
                        master_file.writelines(row)
                    else:
                        count = 1
    create_master_turnstile_file(["turnstile_170624.txt", "turnstile_170617.txt", "turnstile_170610.txt", "turnstile_170603.txt"], "master_file.txt")

### *Exercise 1.3*[¶](#Exercise-1.3) {#Exercise-1.3}

For this exercise, you will write a function that reads the master\_file
created in the previous exercise and load it into a Pandas Dataframe.
This function can be filtered, so that the Dataframe only has lines
where column "DESCn" has the value "Regular".

For example, if the Pandas Dataframe looks like this:

    ,C/A,UNIT,SCP,DATEn,TIMEn,DESCn,ENTRIESn,EXITSn
    0,A002,R051,02-00-00,05-01-11,00:00:00,REGULAR,3144312,1088151
    1,A002,R051,02-00-00,05-01-11,04:00:00,DOOR,3144335,1088159
    2,A002,R051,02-00-00,05-01-11,08:00:00,REGULAR,3144353,1088177
    3,A002,R051,02-00-00,05-01-11,12:00:00,DOOR,3144424,1088231

The Dataframe must look like the following, after filtering only the
lines where column DESCn has the value REGULAR:

    0,A002,R051,02-00-00,05-01-11,00:00:00,REGULAR,3144312,1088151
    2,A002,R051,02-00-00,05-01-11,08:00:00,REGULAR,3144353,1088177

In [6]:

    import pandas

    def filter_by_regular(filename):
        
        turnstile_data = pandas.read_csv(filename)
        turnstile_data = pandas.DataFrame(turnstile_data) 
        turnstile_data = turnstile_data[turnstile_data.DESCn == 'REGULAR']
        
        
        return turnstile_data
    file1 = filter_by_regular("master_file.txt")
    file1.head()

Out[6]:

C/A

UNIT

SCP

STATION

LINENAME

DIVISION

DATEn

TIMEn

DESCn

ENTRIESn

EXITSn

0

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

00:00:00

REGULAR

6224816

2107317

1

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

04:00:00

REGULAR

6224850

2107322

2

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

08:00:00

REGULAR

6224885

2107352

3

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

12:00:00

REGULAR

6225005

2107452

4

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

16:00:00

REGULAR

6225248

2107513

### *Exercise 1.4*[¶](#Exercise-1.4) {#Exercise-1.4}

The NYC Subway data has cumulative entry and exit data in each line.
Let's assume you have a Dataframe called df, which contains only lines
for one particular turnstile (unique SCP, C/A, and UNIT). The following
function must change these cumulative entries for counting all entries
since the last reading (entries from the last line of the Dataframe).

More specifically, there are two things you should do:

1 - Create a new column, called ENTRIESn\_hourly 2 - Insert in this
column the difference between ENTRIESn in the current and the previous
column. If the line has any NAN, fill it out/replace by 1.

Tip: The funtions shift() and fillna() in Pandas might be usefull for
this exercise.

Below you will find and example of how your Dataframe should look by the
end of this exercise:

        C/A  UNIT       SCP     DATEn     TIMEn    DESCn  ENTRIESn    EXITSn  ENTRIESn_hourly
    0     A002  R051  02-00-00  05-01-11  00:00:00  REGULAR   3144312   1088151                1
    1     A002  R051  02-00-00  05-01-11  04:00:00  REGULAR   3144335   1088159               23
    2     A002  R051  02-00-00  05-01-11  08:00:00  REGULAR   3144353   1088177               18
    3     A002  R051  02-00-00  05-01-11  12:00:00  REGULAR   3144424   1088231               71
    4     A002  R051  02-00-00  05-01-11  16:00:00  REGULAR   3144594   1088275              170
    5     A002  R051  02-00-00  05-01-11  20:00:00  REGULAR   3144808   1088317              214
    6     A002  R051  02-00-00  05-02-11  00:00:00  REGULAR   3144895   1088328               87
    7     A002  R051  02-00-00  05-02-11  04:00:00  REGULAR   3144905   1088331               10
    8     A002  R051  02-00-00  05-02-11  08:00:00  REGULAR   3144941   1088420               36
    9     A002  R051  02-00-00  05-02-11  12:00:00  REGULAR   3145094   1088753              153
    10    A002  R051  02-00-00  05-02-11  16:00:00  REGULAR   3145337   1088823              243

In [9]:

    import pandas

    def get_hourly_entries(df):
        df['ENTRIESn_hourly'] = df.ENTRIESn.diff(1)
        df.ENTRIESn_hourly.fillna(1,inplace = True)
        
        
        return df
    file2 = get_hourly_entries(file1)
    file2.head()

Out[9]:

C/A

UNIT

SCP

STATION

LINENAME

DIVISION

DATEn

TIMEn

DESCn

ENTRIESn

EXITSn

ENTRIESn\_hourly

0

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

00:00:00

REGULAR

6224816

2107317

1.0

1

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

04:00:00

REGULAR

6224850

2107322

34.0

2

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

08:00:00

REGULAR

6224885

2107352

35.0

3

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

12:00:00

REGULAR

6225005

2107452

120.0

4

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

16:00:00

REGULAR

6225248

2107513

243.0

### *Exercise 1.5*[¶](#Exercise-1.5) {#Exercise-1.5}

Do the same thing you did in the previous exercise, but taking into
account the exits, column EXITSn. For this, you need to create a column
called EXITSn\_hourly and insert the difference between the column
EXITSn in the current line vs he previous line. If there is any NaN,
fill it out/replace by 0.

In [11]:

    import pandas

    def get_hourly_exits(df):
        df['EXITSn_hourly'] = df.EXITSn.diff(1)
        df.EXITSn_hourly.fillna(1,inplace = True)
        
        
        return df
    file3 = get_hourly_exits(file1)
    file3.head()

Out[11]:

C/A

UNIT

SCP

STATION

LINENAME

DIVISION

DATEn

TIMEn

DESCn

ENTRIESn

EXITSn

ENTRIESn\_hourly

EXITSn\_hourly

0

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

00:00:00

REGULAR

6224816

2107317

1.0

1.0

1

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

04:00:00

REGULAR

6224850

2107322

34.0

5.0

2

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

08:00:00

REGULAR

6224885

2107352

35.0

30.0

3

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

12:00:00

REGULAR

6225005

2107452

120.0

100.0

4

A002

R051

02-00-00

59 ST

NQR456W

BMT

06/17/2017

16:00:00

REGULAR

6225248

2107513

243.0

61.0

### *Exercise 1.6*[¶](#Exercise-1.6) {#Exercise-1.6}

Given an entry variable that represents time, in the format:     
"00:00:00" (hour: minutes: seconds)      Write a function to extract the
hour part from the time in the entry variable And return it as an
integer. For example:          1) if hour is 00, your code must return 0
         2) if hour is 01, your code must return 1          3) if hour
is 21, your code must return 21          Please return te hour as an
integer.

In [2]:

    import pandas
    def time_to_hour(time):
        hour = time.TIMEn.str[-8:-6].astype(int) # your code here
        return hour
    df=pandas.read_csv("master_file.txt");
    print(time_to_hour(df))

    0          0
    1          4
    2          8
    3         12
    4         16
    5         20
    6          0
    7          4
    8          8
    9         12
    10        16
    11        20
    12         0
    13         4
    14         8
    15        12
    16        16
    17        20
    18         0
    19         4
    20         8
    21        12
    22        16
    23        20
    24         0
    25         4
    26         8
    27        12
    28        16
    29        20
              ..
    788184     1
    788185     5
    788186     9
    788187    13
    788188    17
    788189    21
    788190     1
    788191     5
    788192     9
    788193    13
    788194    17
    788195    21
    788196     1
    788197     5
    788198     9
    788199    13
    788200    17
    788201    21
    788202     1
    788203     5
    788204     9
    788205    13
    788206    17
    788207    21
    788208     1
    788209     5
    788210     9
    788211    13
    788212    17
    788213    21
    Name: TIMEn, Length: 788214, dtype: int32

Exercise 2 - Data Analysis[¶](#Exercise-2---Data-Analysis) {#Exercise-2---Data-Analysis}
----------------------------------------------------------

### *Exercise 2.1*[¶](#Exercise-2.1) {#Exercise-2.1}

To understand the relationship between the Subway activity and the
weather, please complete the data from the file already downloaded with
the weather data. We provided you with the file containing NYC weather
data and made it available with the Support Material. You can access it
through the link:
[https://s3.amazonaws.com/content.udacity-data.com/courses/ud359/turnstile\_data\_master\_with\_weather.csv](https://s3.amazonaws.com/content.udacity-data.com/courses/ud359/turnstile_data_master_with_weather.csv)

Now that we have our data in a csv file, write Python code that reads
this file and saves it into a Pandas Dataframe.

Tip:

Use the command below to read the file:

    pd.read_csv('output_list.txt', sep=",")

In [1]:

    import pandas 

    filename = "turnstile_data_master_with_weather.csv"
    filename1 = pandas.read_csv(filename, sep=",")
    filename1 = pandas.DataFrame(filename1)
    filename1.head()

Out[1]:

Unnamed: 0

UNIT

DATEn

TIMEn

Hour

DESCn

ENTRIESn\_hourly

EXITSn\_hourly

maxpressurei

maxdewpti

...

meandewpti

meanpressurei

fog

rain

meanwindspdi

mintempi

meantempi

maxtempi

precipi

thunder

0

0

R001

2011-05-01

01:00:00

1

REGULAR

0.0

0.0

30.31

42.0

...

39.0

30.27

0.0

0.0

5.0

50.0

60.0

69.0

0.0

0.0

1

1

R001

2011-05-01

05:00:00

5

REGULAR

217.0

553.0

30.31

42.0

...

39.0

30.27

0.0

0.0

5.0

50.0

60.0

69.0

0.0

0.0

2

2

R001

2011-05-01

09:00:00

9

REGULAR

890.0

1262.0

30.31

42.0

...

39.0

30.27

0.0

0.0

5.0

50.0

60.0

69.0

0.0

0.0

3

3

R001

2011-05-01

13:00:00

13

REGULAR

2451.0

3708.0

30.31

42.0

...

39.0

30.27

0.0

0.0

5.0

50.0

60.0

69.0

0.0

0.0

4

4

R001

2011-05-01

17:00:00

17

REGULAR

4400.0

2501.0

30.31

42.0

...

39.0

30.27

0.0

0.0

5.0

50.0

60.0

69.0

0.0

0.0

5 rows × 22 columns

### *Exercise 2.2*[¶](#Exercise-2.2) {#Exercise-2.2}

Now, create a function that calculates the number of rainy days. For
this, return the count of the number of days where the column *"rain"*
is equal to 1.

Tip: You might think that interpreting numbers as integers or floats
might not      work at first. To handle this issue, it might be useful
to convert      these numbers into integers. You can do this by writting
cast (column as integer).      So, for example, if we want to launch the
column maxtempi as an integer, we have to      write something like cast
(maxtempi as integer) = 76, instead of just      where maxtempi = 76.

In [10]:

    def num_rainy_days(df):
        count = 0
        for i in df:
            if(int(i) == 1):
                count+=1
        return count
    countR = num_rainy_days(filename1["rain"])
    print(countR)

    44104

### *Exercise 2.3*[¶](#Exercise-2.3) {#Exercise-2.3}

Calculate if the day was cloudy or not (0 or 1) and the maximum
temperature for fog (i.e. the maximum temperature      for cloudy days).

In [19]:

    import pandas as pd
    def max_temp_aggregate_by_fog(df):
        df = df.loc[df["fog"] == 1]
        count = df["maxtempi"].max()

        return count
    file = "turnstile_data_master_with_weather.csv"
    filename1 = pd.read_csv(file);
    output = max_temp_aggregate_by_fog(filename1)
    print(output)

    81.0

### \*Exercise 2.4[¶](#*Exercise-2.4) {#*Exercise-2.4}

Now, calculate the mean for 'meantempi' for the days that are Saturdays
or Sundays (weekend):

In [20]:

    def avg_weekend_temperature(filename):
        filename["DATEn"] = pandas.to_datetime(filename.DATEn)
        aday = (filename.DATEn.dt.weekday_name)
        filename = filename.loc[(aday=="Saturday")|(aday=="Sunday")]
        mean = filename["meantempi"].mean()
        return mean

    filename = "turnstile_data_master_with_weather.csv"
    filename1 = pandas.read_csv(filename)
    output = avg_weekend_temperature(filename1)
    print(output)

    65.10066685403307

### \*Exercise 2.5[¶](#*Exercise-2.5) {#*Exercise-2.5}

Calculate the mean of the minimum temperature 'mintempi' for the days
when the minimum temperature was greater that 55 degrees:

In [21]:

    def avg_min_temperature(filename):

        avg_min_temp_rainy = filename.loc[filename["mintempi"]>55]["mintempi"].mean()
          
        return avg_min_temp_rainy
    filename = "turnstile_data_master_with_weather.csv"
    filename1 = pandas.read_csv(filename)
    output = avg_min_temperature(filename1)
    print(output)

    63.2699012987013

### \*Exercise 2.6[¶](#*Exercise-2.6) {#*Exercise-2.6}

Before you make any analysis, it might be useful to look at the data we
want to analyse. More specifically, we will evaluate the entries by hour
in our data from the NYC Subway to determine the data distribution. This
data is stored in the column ['ENTRIESn\_hourly'].      Draw two
histogramns in the same axis, to show the entries when it's raining vs
when it's not. Below, you will find an example of how to draw
histogramns with Pandas and Matplotlib:     

    Turnstile_weather ['column_to_graph']. Hist ()

In [2]:

    import numpy as np
    import pandas
    import matplotlib.pyplot as plt

    def entries_histogram(turnstile_weather):
        
        
        
        plt.figure()
        turnstile_weather.loc[turnstile_weather['rain'] == 0 ]['ENTRIESn_hourly'].hist(label = "Not Rainy Day")
        turnstile_weather.loc[turnstile_weather['rain'] == 1 ]['ENTRIESn_hourly'].hist(label = "Rainy Day")
        plt.xlabel("Number of entries") 
        plt.ylabel("Total")
        plt.title('entries vs total_count_of_that_entry')
        plt.legend()
        return plt
    filename = "turnstile_data_master_with_weather.csv"
    filename1 = pandas.read_csv(filename)
    output = entries_histogram(filename1)
    output.show()

![](data:image/png;base64,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WLZsGUOGDOGEE06o9cypr33ta43qezGf+9znuP322+nWrRuLFi3iuOOOo0uXLowYMSK3bTSGp7/MrF3YdtttmTRpEtdcc83H1r300kuMGDGCAw44gBEjRvDyyy/zl7/8hTlz5vDd736XwYMH88ILL9TY9oABA+jatStr164F4KabbuLggw9m0KBB/Ou//ivvvPMOkO3dXHVVdqPS8vJyvv/973PIIYew55578qc//QnIksWiRYs2tf3Zz36WxYsX1zq2wYMHc8EFF3DdddcB8Lvf/Y5hw4Zx4IEHMnLkSF599VU+/PBDBgwYwOuvvw7Ahx9+yB577MHf//732ppuMCcVM2s3zjzzTGbOnMm6des2i5911llMmDCBxYsXM378eM4++2wOPfRQjj32WK688koWLVrEpz/96RrbffzxxxkwYAC77rorAMcffzwLFizgySefZJ999uHmm28uWm/jxo08+uijXHvttVx00UUAnH766dxyyy0APP/887z33nsccMABdY7toIMO4tlnnwXgsMMO4+GHH+aJJ55g7NixXHHFFWyzzTacdNJJzJw5E4A//OEPDBo0qN57bfXlpGJm7caOO+7IhAkTmDJlymbx+fPn8+UvfxmAr3zlKzz00EP1au+aa65hr732YtiwYZsdm3nqqaf43Oc+x8CBA5k5cyZPP138fm3HH388AEOGDGHlypUAjBkzhnvuuYcNGzYwbdo0Tj755Hr1pfDeWJWVlRx55JEMHDiQK6+8ctP2Tz31VGbMmAHAtGnTOOWUU+rVdkOUNKlIOkfS05KeknS7pM6S+kt6RNIySXdK2i6V7ZSeL0/r+xW0c36KPyfpyIL4qBRbLum8Uo7FzNqGb33rW9x88828/fbbNZap76m155xzDs899xx33nknEyZM2PQdj5NPPpnrrruOJUuWcOGFF9b43Y9OnToB0KFDBzZu3AhA165d+cIXvsDs2bOZNWvWpmRXlyeeeIJ99tkHgG984xucddZZLFmyhF/84hebtt+3b1/Kysp44IEHeOSRRzjqqKPq1XZDlCypSOoNnA0MjYj9gQ7AWODHwDURMQBYC5yWqpwGrI2IPYBrUjkk7Zvq7QeMAn4uqYOkDsD1wFHAvsC4VNbMrEa9evXixBNP3GxK6tBDD+WOO+4AYObMmRx22GEAdOvWjbfeqvsOwscffzxDhw5l+vTpQPZN9912240NGzZsmm5qiNNPP52zzz6bgw8+mF69etVZfvHixVxyySWceeaZAKxbt47evXsDbOpTYdsnnXQSJ554Ih06dGhw3+pS6rO/tgW6SNoAdAVWA4cD1al3OjAZuAEYnZYB7gKuU/bvwmjgjoh4D3hR0nLgkFRueUSsAJB0Ryr7TInHZGZbqbnPcjz33HM3HdQGmDJlCqeeeipXXnklu+yyC7/61a8AGDt2LGeccQZTpkzhrrvuqvW4ygUXXMCXv/xlzjjjDC655BKGDRvGJz/5SQYOHFivxFRoyJAh7LjjjrVOT/3pT3/isMMOY/369ey6665MmTJl05lfkydPZsyYMfTu3Zvhw4fz4osvbqp37LHHcsopp5Rk6gtKfI96Sd8ELgPeBX4PfBN4OO2NIKkvcF9E7C/pKWBURFSmdS8Aw8gSzcMRcVuK3wzclzYxKiJOT/GvAMMi4qwi/ZgETAIoKysbUv0fSUO9tmYdr77bqKr1NrB399JuoJ6qqqrYYYcdmrsbJddexgnNO9bu3buzxx57NNn2Pvjgg5L8F95UVq9ezdFHH83ChQvZZpuaJ5QaM87HH3+c888/n7lz5xZdv3z58o+dyPD5z39+YUQMrU/7JdtTkdSTbM+hP/Am8P/Ipqq2VJ3Vik1iRi3xYq900QwZEVOBqQBDhw6Nxt4p7WczZ3P1ktLu3K0cX17S9uvLd85re5pzrEuXLm3SCzy25gtKzpgxgx/+8If85Cc/oXv32v/JbOg4L7/8cm644QZmzpxZY73OnTtz4IEHNqjPhUp5oH4k8GJEvB4RG4DfAIcCPSRVfzL3AV5Jy5VAX4C0vjuwpjC+RZ2a4mZmrdaECRNYtWoVY8aMyb3t8847j5deemnTMaNSKGVSeRkYLqlrOjYygux4x4PACanMRGB2Wp6TnpPWPxDZ3NwcYGw6O6w/MAB4FFgADEhnk21HdjB/TgnHY2ZmdSjZXE5EPCLpLuBxYCPwBNkU1H8Dd0i6NMWqT8G4Gbg1HYhfQ5YkiIinJc0iS0gbgTMj4gMASWcBc8nOLJsWEcVPBjczsyZR0gMEEXEhcOEW4RV8dPZWYdn1QNH9vYi4jOyA/5bxe4F7t76nZmaWB3+j3szMcuOrFJtZ05uc86nzk9fVWaRDhw4MHDiQjRs30r9/f2699VZ69OhRa51DDz2Uv/zlL1vdvfLyclavXk2nTp14//33GTlyJJdeemmd22+NvKdiZu1Cly5dWLRoEU899RS9evXi+uuvr7NOHgml2syZM1m8eDGLFy+mU6dOjB49Ore2WxInFTNrdz7zmc/w17/+Fci+FDpixAgOOuggBg4cyOzZszeVq/6yaPV3fE444QT23ntvxo8fT0Rw//338y//8i+bys+bN2/TRSJrst1223HFFVfw8ssv8+STTwJw3HHHMWTIEPbbbz+mTp0KwM0338w555yzqd5NN93Et7/97XxegBJyUjGzduWDDz7g/vvv59hjjwWyL/v99re/5fHHH+fBBx/k3HPPpdiVRp544gmuvfZannnmGVasWMGf//xnDj/8cJYuXbrpHiW/+tWv6nX5kw4dOjBo0KBNl6qfNm0aCxcu5LHHHmPKlCm88cYbjB07ljlz5rBhw4YGtd3cnFTMrF149913GTx4MDvttBNr1qzhC1/4ApBdMv4HP/gBBxxwACNHjuSvf/0rr7766sfqH3LIIfTp04dtttmGwYMHs3LlSiTxla98hdtuu40333yT+fPn1/vKv4WJa8qUKQwaNIjhw4ezatUqli1bxvbbb8/hhx/OPffcw7PPPsuGDRsYOHBgPi9GCflAvZm1C9XHVNatW8cxxxzD9ddfz9lnn83MmTN5/fXXWbhwIR07dtx03/ctVV+mHja/VP0pp5zCl770JTp37syYMWPYdtu6P1Y/+OADlixZwj777ENFRQV/+MMfmD9/Pl27dqW8vHzT9k8//XR+9KMfsffee7eKvRTwnoqZtTPdu3dnypQpXHXVVWzYsIF169ax66670rFjRx588EFeeumlBrW3++67s/vuu3PppZfW64ZaGzZs4Pzzz6dv374ccMABrFu3jp49e9K1a1eeffZZHn744U1lhw0bxqpVq/j1r3/NuHHjGjrUZuE9FTNrevU4BbiUDjzwQAYNGsQdd9zB+PHj+dKXvsTQoUMZPHgwe++9d4PbGz9+PK+//jr77lvzLZ3Gjx9Pp06deO+99xg5cuSmEwJGjRrFjTfeyAEHHMBee+3F8OHDN6t34oknsmjRInr27NngfjUHJxUzaxeqqqo2e/673/1u0/L8+fNrrVNeXr7ZFZ4L78UC8NBDD3HGGWfUuO2Kiooa13Xq1In77ruvxvUPPfTQZmeBtXSe/jIz2wpDhgxh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%0A)

### \*Exercise 2.7[¶](#*Exercise-2.7) {#*Exercise-2.7}

The data you just plotted is in what kind of distribution? Is there a
difference in distribution between rainy and non-rainy days?

**Answer**: The data is positive skewed distribution. Rainy hourly
entries is almost the half of non-rainy hourly enteries

### \*Exercise 2.8[¶](#*Exercise-2.8) {#*Exercise-2.8}

Build a function that returns:

1.  The mean of entries when it's raining
2.  The mean of entries when it's not raining

In [1]:

    import numpy as np

    import pandas

    def means(turnstile_weather):
        rainy = turnstile_weather.loc[turnstile_weather['rain'] == 1 ]['ENTRIESn_hourly']
        sunny = turnstile_weather.loc[turnstile_weather['rain'] == 0 ]['ENTRIESn_hourly']

        return rainy.mean(), sunny.mean()
    filename = "turnstile_data_master_with_weather.csv"
    filename1 = pandas.read_csv(filename)
    output = means(filename1)
    print(output)

    (1105.4463767458733, 1090.278780151855)

Answer to the following questions according to your functions' exits:

1.  What is the mean of entries when it's raining?
2.  What is the mean of entries when it's not raining?

**Answer**: Raining : 1105.4463767458733 ; Non-Raining :
1090.278780151855

Exercise 3 - Map Reduce[¶](#Exercise-3---Map-Reduce) {#Exercise-3---Map-Reduce}
----------------------------------------------------

### *Exercise 3.1*[¶](#Exercise-3.1) {#Exercise-3.1}

The entry for this exercise is the same file from the previous session
(Exercise 2). You can download the file from this link:

[https://s3.amazonaws.com/content.udacity-data.com/courses/ud359/turnstile\_data\_master\_with\_weather.csv](https://s3.amazonaws.com/content.udacity-data.com/courses/ud359/turnstile_data_master_with_weather.csv)

Now, we will create a mapper. For each entry line, the mapper exit must
PRINT (not return) UNIT as a key, and the number of ENTRIESn\_hourly as
the value. Separate the key and the value with a tab. For example: 'R002
\\ t105105.0'

Export your mapper into a file named mapper\_result.txt and send it with
your submission. The code for exporting your mapper is already written
in the code bellow.

In [3]:

    import sys

    def mapper():
        sys.stdin.readline()

        for line in sys.stdin:
            string = line.split(',')
            if len(string) != 22: 
                continue
            print("{0}\t{1}".format(string[1],string[6]))
    sys.stdin = open('turnstile_data_master_with_weather.csv')
    sys.stdout = open('mapper_result.txt', 'w')
    mapper()

### *Exercise 3.2*[¶](#Exercise-3.2) {#Exercise-3.2}

Now, create the reducer. Given the mapper result from the previous
exercise, the reducer must print (not return) one line per UNIT, with
the total number of ENTRIESn\_hourly during May (which is our data
duration), separated by a tab. An example of exit line from the reducer
may look like this: 'R001 \\ t500625.0'

You can assume that the entry for the reducer is ordered in a way that
all lines corresponding to a particular unit are grouped. However, the
reducer exit will have repetition, as there are stores that appear in
different files' locations.

Export your reducer into a file named reducer\_result.txt and send it
with your submission.

In [5]:

    import sys
    def reducer():
        key = None
        countEntries = 0

        for line in sys.stdin:
            string = line.split('\t')
            if len(string)!=2:
                continue
            key2,string1 = string
            if key and key!=key2:
                print(key,'\t',countEntries)
                key = key2
                countEntries=0
            key = key2
            countEntries = countEntries + float(string1)
        if key != None:
            print(key,"\t",countEntries)

            

    sys.stdin = open('mapper_result.txt', 'r').readlines()
    sys.stdout = open('reducer_result.txt', 'w')
    reducer()
