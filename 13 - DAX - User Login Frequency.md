# User Login Frequency

```DAX
First_Login = CALCULATE(MIN('DAX'[LoginDate]),ALLEXCEPT('DAX','DAX'[UserID]))

Last_Login = CALCULATE(MAX('DAX'[LoginDate]),ALLEXCEPT('DAX','DAX'[UserID]))

DAX-Stats = 
ADDCOLUMNS(
    SUMMARIZE('DAX','DAX'[Market],'DAX'[UserID]),
    "First_Login",[First_Login],
    "Last_Login",[Last_Login],
    "Total_Logins",CALCULATE(DISTINCTCOUNT('DAX'[LoginDate]))
)

Avg_Login_Freq = CALCULATE(AVERAGE('DAX-Stats'[Login_Freq]),ALLEXCEPT('DAX-Stats','DAX-Stats'[Market]))

Diff = 'DAX-Stats'[Round_Login_Freq]-[Avg_Login_Freq]

Login_Freq = 'DAX-Stats'[Total_Months]/'DAX-Stats'[Total_Logins]

Pct = CALCULATE(DISTINCTCOUNT('DAX-Stats'[UserID]))/[Total_Users]

Round_Login_Freq = ROUND('DAX-Stats'[Login_Freq],0)

Total_Months = DATEDIFF('DAX-Stats'[First_Login],'DAX-Stats'[Last_Login],MONTH)

Total_Users = CALCULATE(DISTINCTCOUNT('DAX-Stats'[UserID]),ALLEXCEPT('DAX-Stats','DAX-Stats'[Market]))
```

# Result
