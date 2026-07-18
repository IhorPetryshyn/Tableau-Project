## 🗄️ Data Preparation (SQL)

```sql
with union_data as(
    SELECT
            DATE_ADD(ss.date, INTERVAL s.sent_date DAY ) as date,
            sp.country,
            count(distinct s.id_message) as sent_cnt,
            count(distinct o.id_message) as open_cnt,
            count(distinct v.id_message) as click_cnt,
            null as account_cnt
FROM `DA.email_sent` s
left join `DA.account_session` ac
on s.id_account = ac.account_id
left join `DA.session_params` sp
on ac.ga_session_id = sp.ga_session_id
left join `DA.email_open` o
on s.id_message = o.id_message
left join `DA.email_visit` v
on o.id_message = v.id_message
join `DA.session` ss
on ac.ga_session_id = ss.ga_session_id
group by sp.country, DATE_ADD (ss.date, INTERVAL s.sent_date DAY ) 
union all
Select
        ss.date,
        sp.country,
        null, null, null,
        count(ac.account_id) as account_cnt
from `DA.account_session` ac
left join `DA.session_params` sp
on ac.ga_session_id = sp.ga_session_id
join `DA.session` ss
on ac.ga_session_id = ss.ga_session_id
group by ss.date,
        sp.country
)

SELECT
        date,
        country,
        sum(sent_cnt) as sent_cnt, 
        sum(open_cnt) as open_cnt,
        sum(click_cnt) as clicn_cnt,
        sum(account_cnt) as account_cnt,
from union_data
group by date, country
```
# Email Metrics Dashboard

An interactive dashboard designed to analyze email marketing effectiveness (Open Rate, Click Rate, CTOR).

## 📊 Interactive Version
You can view and interact with the dashboard directly on Tableau Public:
* 👉 [View Email Metrics on Tableau Public](https://public.tableau.com/app/profile/ihor.petrsyhyn/viz/EmailMetrics_17785785471980/EmailMetrics?publish=yes)

## 📷 Скріншот дашборду
<img width="1199" height="1199" alt="Tableau Project" src="https://github.com/user-attachments/assets/6f2641fe-5162-43dd-8288-99786aa988c6" />


## 🛠️ Metrics & Functionality Overview
* **Open Rate:** The percentage of sent emails that were opened (~35.49%).
* **Click Rate:** The percentage of links clicked out of total sent emails (~3.85%).
* **CTOR (Click-to-Open Rate):** The ratio of clicks to unique opens, measuring content relevance (~10.86%).
* **Data Filtering:** Dynamic filtering options by **Country** and time periods (**Date/Year**).
