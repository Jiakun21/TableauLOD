# User Login Frequency

```SQL
-- Total_Months = Last_Login - First_Login
-- Login_Freq = Total_Months / Total_Logins

With Stats as (
Select 
    UserID, Market,
    Min(LoginDate) as First_Login,
    Max(LoginDate) as Last_Login,
    Period_Diff(Date_Format(Max(LoginDate),'%Y%m'), Date_Format(Min(LoginDate),'%Y%m')) as Total_Months,
    Count(Distinct LoginDate) as Total_Logins
From lod13
Group by UserID，Market)

Select 
    s.Market,
    Round(Total_Months/Total_Logins) as Login_Freq, 
    Round(Count(Distinct UserID)/Total_Users * 100, 2) as Pct,
    Round(Avg_Login_Freq, 3) as Avg_Login_Freq
From Stats s Left Join 
                (Select 
                     Market, 
                     Avg(Total_Months/Total_Logins) as Avg_Login_Freq,
                     Count(Distinct UserID) as Total_Users
                 From Stats
                 Group by Market) t Using(Market)
Group by s.Market, Round(Total_Months/Total_Logins)
```
