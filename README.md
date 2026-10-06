****Optimizing Sky Traffic: U.S. Airline Delay Forecasting (2020–2026)****
Forecasting monthly arrival delay minutes for U.S. airlines, finding what drives delay, and testing a what-if scenario. Built in Python and validated on future data the model never saw, then benchmarked against simple baselines.

**Key results**
Test period: January 2025 to July 2026 (35,632 records). Training period: July 2020 to December 2024 (101,060 records).

Model	MAE (min)	RMSE (min)	R²	WAPE

XGBoost + CatBoost blend (raw minutes)	1,494	5,273	0.912	26.6%

Baseline: last month's delay	1,976	6,387	0.871	35.2%

XGBoost, original 8 features	1,793	6,847	0.852	31.9%

Blend trained on log(delay)	1,603	7,189	0.837	28.5%

Baseline: same month last year	2,156	7,337	0.830	38.4%

Baseline: linear on flight count only	2,149	8,165	0.789	38.2%

WAPE = total absolute error ÷ total actual delay minutes.

The final model has about 24% lower MAE and 17% lower RMSE than the strongest baseline.

**Main findings**
1.Forecasting works, with realistic accuracy. On unseen 2025–2026 data the final model reaches R² 0.912 and beats every baseline on every metric.

2.Recent history is the strongest signal. Permutation importance ranks last month's delay first, then flight volume, then the 3-month rolling average. Carrier and the same-month-last-year delay come next. Holiday and quarter flags add almost nothing.

3.History beats volume alone. Predicting from flight count only gives R² 0.789, so knowing a carrier-airport pair's recent delay adds a lot.

4.A random split overstated the original result. With the same model and features, a random split gave R² 0.877 and MAE 1,222, while a time-based split gave R² 0.852 and MAE 1,793. Only the time-based number reflects forecasting the future.

5.The log transform caused a bias. Training on log(delay) made the models predict only about 87% of actual total delay. Training on raw minutes raised test R² from 0.837 to 0.912.

6.Errors concentrate at the biggest hubs. ORD, ATL, DFW, DEN, and CLT produce the largest misses in minutes, and OO, AA, WN, DL, and UA are the carriers with the most total error. Relative accuracy is best for the busiest routes (WAPE 23.9%) and worst for the smallest (WAPE 71.2%, on small absolute errors).

7.Results are stable across test years. WAPE is 26.4% in 2025 and 26.9% in the partial 2026.

8.Most delay is controllable (from the Version 1 analysis). Roughly 70–77% of delay minutes each year came from carrier and late-aircraft causes, and American Airlines at DFW was the top delay-producing carrier-airport pair every year.

**How these findings help**

1.Planning ahead. Forecasting next month's delay per carrier-airport pair lets teams plan staffing, schedule buffers, and passenger communication, with about 24% less error than assuming next month looks like this one.

2.Choosing where to focus. A few large hubs carry most of the absolute delay and error, so effort there has the largest effect.

3.Choosing what to monitor. Recent delay and flight volume carry most of the predictive signal, so a simple dashboard on those two covers most of what matters.

4.Separating fixable from unfixable delay. The controllable share (about 70–77%) is the part airlines can act on, unlike weather or air-traffic control.

5.Knowing where to be careful. Predictions for small routes are the least reliable in relative terms and should be treated with more caution.

6.Honest evaluation. The time-based split and baselines show what the model can really do, instead of a number that looks better than it is.

**Repository contents**

*File	Description*

1.Optimizing_Sky_Traffic.ipynb	Main notebook. Time-based validation, baselines, lag features, tuning, feature importance, error analysis

2.Airline_Delay_Cause.csv	Dataset (one row per carrier, airport, and month)
Dataset

3.U.S. airline on-time performance and delay causes, aggregated by carrier × airport × month, July 2020 to July 2026 (136,876 rows).

4.The target is arr_delay: total arrival delay minutes for that carrier-airport-month.

5.184 rows with a missing target or flight count were dropped, not imputed, leaving 136,692.

2026 is a partial year (data ends in July).

**Method**

1.Time-based split. Train on 2020–2024 and test on 2025 onward.

2.Lag and rolling features. For each carrier-airport pair: delay 1, 2, 3, and 12 months earlier, a 3-month rolling average, last month's flight volume, and last month's delay per flight. All use past months only.

3.Baselines. Linear regression on flight count, last month's delay, and the same month last year.

4.Tuning. Randomized search over XGBoost and CatBoost settings with TimeSeriesSplit cross-validation, so tuning also respects time order.

5.Target choice. Both a log target and a raw-minute target were tuned and compared; the raw-minute target won.

6.Final model. Simple average of the tuned XGBoost and CatBoost predictions.

7.Interpretation. Permutation importance, SHAP, and an error breakdown by flight volume, year, month, airport, and carrier.

**Limitations**

1.Aggregated data. The model forecasts monthly totals per carrier-airport pair, not delays of individual flights.

2.Light tuning. 12 XGBoost and 6 CatBoost candidates were searched, so a wider search may improve results slightly.

3.Correlation, not cause. The Version 1 what-if scenario (15% lower flight volume) shows how the model responds to volume.

4.It is not evidence that a specific operational change would produce those savings.

5.Partial final year. 2026 covers January to July only.

**Tech stack**

Python, Pandas, NumPy, Scikit-Learn, XGBoost, CatBoost, SHAP, Matplotlib, Seaborn, Jupyter

**How to run**

bash

git clone https://github.com/aalaya24062001-debug/Optimizing-Sky-Traffic.git

cd Optimizing-Sky-Traffic

pip install pandas numpy scikit-learn xgboost catboost shap matplotlib seaborn jupyter

jupyter notebook Optimizing_Sky_Traffic_Improved.ipynb

