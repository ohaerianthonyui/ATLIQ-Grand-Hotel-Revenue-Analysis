# ATLIQ-Grand-Hotel-Revenue-Analysis
Provide Insights to the Revenue Team in the Hospitality Domain

AtliQ Grands owns multiple five-star hotels across India. They have been in the hospitality industry for the past 20 years. Due to strategic moves from other competitors and ineffective decision-making in management, AtliQ Grands are losing its market share and revenue in the luxury/business hotels category. As a strategic move, the managing director of AtliQ Grands wanted to incorporate “Business and Data Intelligence” to regain their market share and revenue. However, they do not have an in-house data analytics team to provide them with these insights.

Their revenue management team had decided to hire a 3rd party service provider to provide them with insights from their historical data.

Task:  

Create the metrics according to the metric list.
Create relevant insights that are not provided in the metric list.

## Tools and Methodology

1	wn	"To get the week number from the corresponding date. 

"	wn = WEEKNUM(dim_date[date])	dim_date
2	day type	"Based on the feedback from stakeholder, we considered 
Friday and Saturday as weekend and weekdays from Sunday to Thurdsay. In PowerBI, Sunday weekday number is 1, Monday is 2 and so on. So, if weekday number is greater than 5, then weekend or else weekday.

https://learn.microsoft.com/en-us/dax/weekday-function-dax
"	"day type = 
 
 Var wkd = WEEKDAY(dim_date[date],1)

 return
 IF(
 wkd>5,""Weekend"",""Weekday"")"	dim_date
				
				
				
### Measures:				

1	Revenue	To get the total revenue_realized	Revenue = SUM(fact_bookings[revenue_realized])	fact_bookings

2	Total Bookings	To get the total number of bookings happened	Total Bookings = COUNT(fact_bookings[booking_id])	fact_bookings

3	Total Capacity	To get the total capacity of rooms present in hotels	Total Capacity = SUM(fact_aggregated_bookings[capacity])	fact_aggregated_bookings

4	Total Succesful Bookings	To get the total succesful bookings happened for all hotels	Total Succesful Bookings = SUM(fact_aggregated_bookings[successful_bookings])	fact_aggregated_bookings

5	Occupancy %	"Occupancy means total successful bookings happened to the total rooms available(capacity)"	Occupancy % = DIVIDE([Total Succesful Bookings],[Total Capacity],0)	fact_aggregated_bookings

6	Average Rating	Get the average ratings given by the customers	Average Rating = AVERAGE(fact_bookings[ratings_given])	fact_bookings

7	No of days	"To get the total number of days present in the data.

In our case, we have data from May to July. So 92 days. "	No of days = DATEDIFF(MIN(dim_date[date]),MAX(dim_date[date]),DAY) +1	dim_date

8	Total cancelled bookings	To get the"Cancelled" bookings out of all Total bookings happened	Total cancelled bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="Cancelled")	fact_bookings

9	Cancellation %	"calculating the cancellaton percentage. "	Cancellation % = DIVIDE([Total cancelled bookings],[Total Bookings])	fact_bookings

10	Total Checked Out	To get the successful 'Checked out' bookings out of all Total bookings happened	Total Checked Out = CALCULATE([Total Bookings],fact_bookings[booking_status]="Checked Out")	fact_bookings

11	Total no show bookings	"To get the""No Show"" bookings out of all Total bookings happened  (""No show"" means those customers who neither cancelled nor attend to their booked rooms)"	Total no show bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="No Show")	fact_bookings

12	No Show rate %	calculating the no show percentage.	No Show rate % = DIVIDE([Total no show bookings],[Total Bookings])	fact_bookings

13	Booking % by Platform	"To show the percentage contribution of each booking platform for bookings in hotels.

We have booking platforms like makeyourtrip, logtrip, tripster etc)"	"Booking % by Platform = DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(fact_bookings[booking_platform])
  ))*100"	fact_bookings

  
14	Booking % by Room class	"To show the percentage contribution of each room class
over total rooms booked.

We have room classes like Standard, Elite, Premium, Presidential."	"Booking % by Room class = DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(dim_rooms[room_class])
  ))*100"	fact_bookings, dim_rooms

  
15	ADR 	"Calculate the ADR(Average Daily rate)

It is the ratio of revenue to the total rooms booked/sold. 
It is the measure of the average paid for rooms sold in a given time period"	ADR = DIVIDE( [Revenue], [Total Bookings],0)	fact_bookings

16	Realisation %	"calculate  the realisation percentage. It is nothing but the succesful ""checked out"" percentage over all bookings happened.

"	Realisation % = 1- ([Cancellation %]+[No Show rate %])	fact_bookings

17	RevPAR	"Calculate the RevPAR(Revenue Per Available Room)

RevPAR represents the revenue generated per available room, whether or not they are occupied. RevPAR helps hotels measure their revenue generating performance to accurately price rooms. RevPAR can help hotels measure themselves against other properties or brands."	RevPAR = DIVIDE([Revenue],[Total Capacity])	fact_bookings, fact_agg_bookings

18	DBRN	"calculate DBRN(Daily Booked Room Nights)

This metrics tells on average how many rooms are booked for a day considering a time period

"	DBRN = DIVIDE([Total Bookings], [No of days])	fact_bookings, dim_date

19	DSRN 	"calculate DSRN(Daily Sellable Room Nights)

This metrics tells on average how many rooms are ready to sell for a day considering a time period

"	DSRN = DIVIDE([Total Capacity], [No of days])	fact_agg_bookings, dim_date

20	DURN	"calculate DURN(Daily Utilized Room Nights)

This metric tells on average how many rooms are succesfully utilized by customers for a day considering a time period
"	DURN = DIVIDE([Total Checked Out],[No of days])	fact_bookings, dim_date

21	Revenue WoW change %	"To get the revenue change percentage week over week.

Here, 
revcw  for current week
revpw for previous week


"	"Revenue WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Revenue],dim_date[wn]= selv)
var revpw =  CALCULATE([Revenue],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"	dim_date

22	Occupancy WoW change %	"To get the occupancy change percentage week over week.
Here, 
revcw  for current week
revpw for previous week"	"Occupancy WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Occupancy %],dim_date[wn]= selv)
var revpw =  CALCULATE([Occupancy %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"	dim_date

23	ADR WoW change %	"To get the ADR(Average Daily rate) change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"ADR WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([ADR],dim_date[wn]= selv)
var revpw =  CALCULATE([ADR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"	dim_date

24	Revpar WoW change %	"To get the RevPar(Revenue Per Available Room) change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"Revpar WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([RevPAR],dim_date[wn]= selv)
var revpw =  CALCULATE([RevPAR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"	dim_date

25	Realisation WoW change %	"To get the Realisation change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"Realisation WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Realisation %],dim_date[wn]= selv)
var revpw =  CALCULATE([Realisation %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"	dim_date

26	DSRN WoW change %	"To get the DSRN(Daily Sellable Room Nights) change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"DSRN WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([DSRN],dim_date[wn]= selv)
var revpw =  CALCULATE([DSRN],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"





#### FINDINGS


---

### **Hotel Performance & Revenue Analysis**

#### **Key Metrics Overview**
- **Total Revenue**: $1.71 billion
- **RevPAR (Revenue per Available Room)**: $7,350
- **Revenue Week-over-Week (WOW) Change**: -81.74%
- **Total Bookings**: 135,000
- **Occupancy Rate**: 57.87%
- **Average Daily Rate (ADR)**: $12,700
- **Daily Sellable Room Nights**: 2,530
- **Daily Booked Room Nights**: 1,460

#### **Revenue Insights by Time Period and Location**
- **Highest Revenue by Week**:
  - **Weeks 18–20**: $140 million
  - **Lowest Revenue by Week**:
  - **Week 31**: $20 million
  
- **Revenue Insights by City**:
  - **Mumbai**: Highest revenue with $670 million
  - **Delhi**: Lowest revenue with $290 million

- **Revenue by Month**:
  - **May**: Highest revenue with $582 million
  - **June**: Lowest revenue with $554 million

#### **Revenue per Room Insights**
- **Highest Revenue per Room**:
  - **Mumbai**: $8,900 per room
- **Lowest Revenue per Room**:
  - **Hyderabad**: $5,400 per room

#### **Room Category Performance**
- **Highest Revenue by Room Category**:
  - **Business Category**: Highest revenue
- **Highest Capacity and Bookings by Room Category**:
  - **Luxury Category**: Highest capacity and bookings

#### **Booking Insights**
- **Booking Platforms**:
  - **Logtrip Booking Platform**: Highest realization percentage with 14.36%
  - **Tripster Booking Platform**: Lowest realization percentage

- **Booking and Cancellation Trends**:
  - People preferred making and canceling bookings through **third-party platforms**.

- **Weekends**: Highest occupancy percentage by day.

#### **Additional Key Metrics**
- **Show Rate**: 5.02%
- **Occupancy Percentage**: 57.87%
- **Average Daily Rate (ADR)**: $12,700

---

### **Conclusion**

The hotel performance indicates a strong but fluctuating revenue trajectory. Notably, revenue saw a significant **week-over-week decrease of 81.74%**, which may require further investigation to understand the underlying causes. Despite this, **Mumbai** emerged as the strongest performer in terms of total revenue and revenue per room, while **Delhi** lagged behind. The **Luxury** category led in both room capacity and bookings, while **Business** rooms generated the highest revenue.

The highest revenue-generating weeks were **Weeks 18 to 20**, with **May** generating the highest monthly revenue, highlighting potential seasonal trends. On the flip side, **June** experienced the lowest revenue, which could point to seasonality or other market factors. Additionally, the **Logtrip platform** showed the highest booking realization percentage, suggesting more effective pricing strategies or higher customer engagement compared to **Tripster**.

The occupancy rate of **57.87%** indicates potential for growth, particularly considering the higher occupancy observed on weekends. The **average daily rate (ADR)** of **$12,700** is strong, though it may be beneficial to review pricing strategies and optimize revenue management further.

---

### **Recommendations**

1. **Revenue Optimization**:
   - Investigate the causes behind the **81.74% week-over-week revenue decrease**, as this significant drop can indicate operational issues or market shifts.
   - Focus on enhancing the performance of **Delhi**, which has the lowest revenue, by investigating local demand, customer preferences, and competitive pricing strategies.

2. **Booking Platforms**:
   - **Logtrip** shows a higher realization percentage, suggesting that **more aggressive marketing** or **better pricing strategies** on this platform might be working. Explore opportunities to boost performance on the **Tripster platform** by analyzing its booking patterns and improving conversion rates.

3. **Room Category Focus**:
   - **Business rooms** generated the highest revenue, indicating a strong market for business travelers. Tailor promotions and pricing for the **Business category** to further capitalize on this demand.
   - The **Luxury room category** has the highest bookings and capacity, making it essential to **maintain high service standards** and offer **exclusive packages** to retain and attract more guests in this segment.

4. **Occupancy Rate Improvement**:
   - **Weekend occupancy rates** are higher, suggesting demand spikes on weekends. Consider introducing **weekend promotions** or packages to increase **weekend bookings** while driving occupancy on weekdays.

5. **Pricing Strategy Review**:
   - Review the **Average Daily Rate (ADR)** and **Revenue per Room** metrics across cities. **Mumbai's ADR** is significantly higher than **Hyderabad**, indicating potential for increasing room rates in lower-performing cities through targeted marketing or enhancing the guest experience.

6. **Operational Efficiency**:
   - Continue to monitor and optimize **Daily Sellable Room Nights** (2,530) to ensure room availability is aligned with demand trends, focusing on improving operational efficiency and reducing no-shows and cancellations.

7. **Seasonal Strategy**:
   - Given the strong performance in **May**, but a drop in **June**, it is essential to implement a **seasonal strategy**. Consider promoting events, packages, and local attractions during months with historically lower performance.

---

By focusing on these key areas, the hotel can certainly improve its revenue performance, occupancy, and overall guest satisfaction, creating a more profitable business strategy.

