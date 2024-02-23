import pandas as pd
import numpy as np

def fvg_strategy(df, timeframe, symbol):
  """
  استراتژی FVG در FVG

  آرگومان ها:
    df: دیتافریم قیمت
    timeframe: تایم فریم
    symbol: نماد

  برمی گرداند:
    لیست سیگنال های معاملاتی
  """

  # محاسبه FVG
  df['fvg'] = np.where((df['High'] - df['Low']) > (df['Open'] - df['Close']), True, False)

  # فیلتر کردن FVG ها
  df = df[df['fvg']]

  # سیگنال های معاملاتی
  signals = []

  for i in range(len(df)):
    if df['fvg'].iloc[i]:
      # معامله در لبه FVG
      if i > 0 and df['Close'].iloc[i] > df['Close'].iloc[i-1]:
        signals.append({'type': 'buy', 'price': df['Open'].iloc[i], 'timeframe': timeframe, 'symbol': symbol})
      elif i > 0 and df['Close'].iloc[i] < df['Close'].iloc[i-1]:
        signals.append({'type': 'sell', 'price': df['Open'].iloc[i], 'timeframe': timeframe, 'symbol': symbol})

  return signals

# مثال استفاده
df = pd.read_csv('data.csv', index_col='Date')

signals = fvg_strategy(df, timeframe='1H', symbol='EURUSD')

print(signals)
