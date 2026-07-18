## 🗄️ Підготовка даних (SQL)

Для розрахунку основних метрик ефективності розсилок та підготовки фінального набору даних для Tableau, був використаний наступний SQL-запит:
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

Інтерактивний дашборд для аналізу ефективності email-маркетингу (Open Rate, Click Rate, CTOR) за 2020-2021 роки.

## 📊 Інтерактивна версія
Ви можете переглянути та потестувати дашборд безпосередньо на Tableau Public:
👉 [Переглянути Email Metrics на Tableau Public](https://public.tableau.com/app/profile/ihor.petrsyhyn/viz/EmailMetrics_17785785471980/EmailMetrics?publish=yes)

## 📷 Скріншот дашборду
![Email Metrics Dashboard](https://drive.google.com/file/d/1N30786BoDE0roK5wxyVZjLSfE5rHuL-5/view?usp=sharing)

## 🛠️ Опис метрик та функціоналу
* **Open Rate:** Відсоток відкритих листів (~35.49%).
* **Click Rate:** Відсоток кліків (~3.85%).
* **CTOR (Click-to-Open Rate):** Співвідношення кліків до відкриттів (~10.86%).
* **Фільтрація:** Можливість фільтрувати дані за країнами (Country) та часовим періодом (Date/Year).
