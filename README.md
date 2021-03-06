# AS OF NOW THE SCRIPT IS NOT WORKING, DUE TO MAKING TOO MANY API REQUESTS. I AM TOO CHEAP TO BUY AN API LICENSE TO FIX IT. IT'S VERY NEARLY COMPLETE IF YOU WANT TO TRY IT YOURSELF.

# PiFolio Monitor

This program allows you to visualize your portfolio value changes on your Raspberry Pi Sense HAT. Whenever you toggle the joystick up on the HAT, it will light up showing you how the value of your portfolio has changed throughout the day. 

**Required Packages and Comments About Files**  
I've uploaded the requirements.txt file for the Python environment I used for this progmam. I am certain alpha_vantage, datetime, and sense-hat were crucial, but I'm not sure about the others. I tried removing urllib3 from the environment, and then the program started returning a lot of errors. So I've kept it in for now, but if you know how to slim down the package requirements, submit a pull request.

**API Key**  
The TimeSeries function takes an API key as input. You can get your own key from AlphaVantage.co.

**Setting the Variables**  
You need to fill the list "portfolio" with the stocks you own and the number of shares. An example might look like this:

    portfolio=[["TSLA",20],["OLED",5],["AAPL",10]]

**Reading the Matrix**  
Assuming the 8x8 light matrix on the Sense HAT, the middle line between rows 3 and 4 on the y-axis is a 0% change in portfolio value. Columns indicate hours in the market day (0 being 9:30, 6 being 15:30). Lights are OFF by default. For value changes between -3.5% and +3.5% the lights are YELLOW. Row 3 lights indicate a value change between +0% and +0.5%. Row four lights indicate a value change of -0% to -0.5%. Every row beyond that is a 1% increment (ex: row 2 indicates a value change between +0.5% to +1.5% change).

Example Matrix A shows the following values, where N is NULL and Y is YELLOW: (9:30, 1.3%) (10:30, 2.6%) (11:30, -1.9%)

       0  1  2  3  4  5  6  7  
    0  N  Y  N  N  N  N  N  N  
    1  N  N  N  N  N  N  N  N  
    2  Y  N  N  N  N  N  N  N  
    3  N  N  N  N  N  N  N  N  
    4  N  N  N  N  N  N  N  N  
    5  N  N  N  N  N  N  N  N  
    6  N  N  Y  N  N  N  N  N  
    7  N  N  N  N  N  N  N  N  

If there is a value change more than 3.5% (positive or negative) the matrix changes. 

If the value changes more than +3.5%, the column light becomes GREEN starting at the bottom row, but continuing the 1% increments. Previous columns that were less than +3.5% (but more than -3.5%) illuminate YELLOW in the bottom row of their column.

Example Matrix B shows the following values, where N is NULL, Y is YELLOW, and G is GREEN: (9:30, 1.3%) (10:30, 2.6%) (11:30, -1.9%) (12:30, 4%) (13:30, 5%)

       0  1  2  3  4  5  6  7  
    0  N  N  N  N  N  N  N  N  
    1  N  N  N  N  N  N  N  N  
    2  N  N  N  N  N  N  N  N  
    3  N  N  N  N  N  N  N  N  
    4  N  N  N  N  N  N  N  N  
    5  N  N  N  N  N  N  N  N  
    6  N  N  N  N  G  N  N  N  
    7  Y  Y  Y  G  N  N  N  N  

If the values changes more than -3.5%, the column light becomes RED starting at the top row, but continuing the 1% increments. Previous columns that were less than +3.5% and more than -3.5% are illuminated YELLOW in the top row of their column. Previous columns that were greater than +3.5% illuminate GREEN in the top row of their column.

Example Matrix C shows the following values, where N is NULL, Y is YELLOW, G is GREEN, and R is RED: (9:30, 1.3%) (10:30, 2.6%) (11:30, -1.9%) (12:30, 4%) (13:30, 5%) (14:30, -5%) (15:30, -8%)

       0  1  2  3  4  5  6  7  
    0  Y  Y  Y  G  G  N  N  N  
    1  N  N  N  N  N  R  N  N  
    2  N  N  N  N  N  N  N  N  
    3  N  N  N  N  N  N  N  N  
    4  N  N  N  N  N  N  R  N  
    5  N  N  N  N  N  N  N  N  
    6  N  N  N  N  N  N  N  N  
    7  N  N  N  N  N  N  N  N  
