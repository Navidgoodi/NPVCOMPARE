import streamlit as st
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

st.set_page_config(page_title="Car Strategy Optimizer", layout="wide")

st.title("🚗 داشبورد استراتژی بهینه خرید خودرو")

st.sidebar.header("🕹️ تنظیمات مدل")
r_percent = st.sidebar.slider("نرخ تنزیل (Discount Rate %)", 0, 50, 25)
r = r_percent / 100  


COST = 15.0

def calculate_all_paths(r):
    npv_y0 = -COST + sum([5 / (1 + r)**t for t in range(1, 10)])

    npv_y3 = sum([3.733 / (1 + r)**t for t in range(1, 4)]) - (COST / (1 + r)**3) + \
             sum([6.5 / (1 + r)**t for t in range(4, 10)])
    
    npv_y6 = sum([3.733 / (1 + r)**t for t in range(1, 4)]) + \
             sum([2.5 / (1 + r)**t for t in range(4, 7)]) - (COST / (1 + r)**6) + \
             sum([6.5 / (1 + r)**t for t in range(7, 10)])
                  
    npv_keep = sum([3.733 / (1 + r)**t for t in range(1, 4)]) + \
               sum([2.5 / (1 + r)**t for t in range(4, 7)]) + \
               sum([1 / (1 + r)**t for t in range(7, 10)])
    
    return [npv_y0, npv_y3, npv_y6, npv_keep]


y6_buy_val = -COST + sum([6.5 / (1 + r)**t for t in range(1, 4)])
y6_keep_val = sum([1 / (1 + r)**t for t in range(1, 4)])
best_at_y6 = max(y6_buy_val, y6_keep_val)


y3_buy_val = -COST + sum([6.5 / (1 + r)**t for t in range(1, 7)])
y3_wait_val = sum([2.5 / (1 + r)**t for t in range(1, 4)]) + (best_at_y6 / (1 + r)**3)
best_at_y3 = max(y3_buy_val, y3_wait_val)


y0_buy_val = -COST + sum([5 / (1 + r)**t for t in range(1, 10)])
y0_wait_val = sum([3.733 / (1 + r)**t for t in range(1, 4)]) + (best_at_y3 / (1 + r)**3)
final_best_val = max(y0_buy_val, y0_wait_val)


npvs = calculate_all_paths(r)
path_names = ["buy at 1st year", "buy at 3rd year", 'buy at 6th year', "Hold"]
best_index = npvs.index(max(npvs))

col1, col2, col3, col4 = st.columns(4)
for i, col in enumerate([col1, col2, col3, col4]):
    col.metric(path_names[i], f"{round(npvs[i], 2)}")

st.success(f"🎯 **بهترین استراتژی در نرخ {r_percent}%:** {path_names[best_index]}")


st.subheader("Compare NPV Values")
rates_range = np.linspace(0, 0.5, 100)
chart_data = []
for rr in rates_range:
    chart_data.append(calculate_all_paths(rr))

df_chart = pd.DataFrame(chart_data, columns=path_names)
df_chart['Rate'] = rates_range * 100

fig, ax = plt.subplots(figsize=(10, 5))
for name in path_names:
    linewidth = 4 if name == path_names[best_index] else 2
    ax.plot(df_chart['Rate'], df_chart[name], label=name, linewidth=linewidth)

ax.axvline(x=r_percent, color='black', linestyle='--', alpha=0.5, label=f'Selected Rate ({r_percent}%)')
ax.set_xlabel("Discount Rate (%)")
ax.set_ylabel("Net Present Value (NPV)")
ax.legend()
ax.grid(True, alpha=0.3)
st.pyplot(fig)


st.subheader("📋 جدول مقایسه دقیق")
df_res = pd.DataFrame({
    "استراتژی": path_names,
    "NPV نهایی": [round(v, 3) for v in npvs]
})
st.table(df_res)

