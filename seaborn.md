# seaborn
```py
# 特徴量同士の相関をヒートマップで表示
import seaborn as sns
import matplotlib.pyplot as plt
fig, ax = plt.subplots(figsize=(20,20))
sns.heatmap(df_corr, square=True, vmax=1, vmin=-1, center=0)
```
