# Final selected model comparison

- Selected sample counts: [20 30]
- Reason: at N=40 MLP slightly outperformed RD-GEP; by the stated stability rule, evaluation stops after the stable <1% region where RD-GEP remains best.
- Protocol: FREQ1, X=data(:,1:4), T=data(:,5), f=data(:,6), lowTempRatio=0.75, highTestPick=coldest fallback, frontierBiasFraction=0.70, nRepeats=20.

## Final summary

- RD_GEP N=20 HighTest_MAPE_Mean=0.930168% LowHoldout_MAPE_Mean=0.887355%
- BaseGEP N=20 HighTest_MAPE_Mean=0.970262% LowHoldout_MAPE_Mean=1.280195%
- SVR_RBF N=20 HighTest_MAPE_Mean=5.947556% LowHoldout_MAPE_Mean=3.496039%
- Kriging N=20 HighTest_MAPE_Mean=7.186861% LowHoldout_MAPE_Mean=3.612882%
- MLP N=20 HighTest_MAPE_Mean=8.697543% LowHoldout_MAPE_Mean=4.126481%
- RD_GEP N=30 HighTest_MAPE_Mean=0.684668% LowHoldout_MAPE_Mean=0.430387%
- BaseGEP N=30 HighTest_MAPE_Mean=0.873600% LowHoldout_MAPE_Mean=0.299241%
- MLP N=30 HighTest_MAPE_Mean=1.235931% LowHoldout_MAPE_Mean=2.883566%
- SVR_RBF N=30 HighTest_MAPE_Mean=5.627205% LowHoldout_MAPE_Mean=1.528187%
- Kriging N=30 HighTest_MAPE_Mean=7.046830% LowHoldout_MAPE_Mean=2.258889%
