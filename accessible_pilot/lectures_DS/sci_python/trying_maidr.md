# Trying MAIDR (a pilot)

This page is an **experiment**, and we'd love your feedback on it.

Starting in this section, the course uses **plots** — which a screen reader can't read on
its own. **MAIDR** is a tool that turns a Matplotlib chart into something you *can* explore:
by **sound** (sonification — you hear the data as a changing pitch), by **text** (read each
value), and by **braille** (on a refreshable braille display) — all keyboard-navigable.

It's research software and still fairly new, so some things may be rough. Trying it and
telling us what helps (and what doesn't) is exactly the point.

## Install it

In your terminal:

~~~
pip install -U maidr
~~~

## Try it: the Mauna Loa CO₂ curve

This plots the atmospheric CO₂ record you met in the Data section and opens it as an
accessible chart. Put it in a file called `maidr_co2.py` and run it with
`python maidr_co2.py`:

~~~python
import pandas as pd
import matplotlib.pyplot as plt
import maidr

url = "https://raw.githubusercontent.com/earth-DS-ML/summer_2026/refs/heads/main/lectures_DS/data/monthly_in_situ_co2_mlo.csv"
columns = ["year", "month", "date_excel", "date", "co2", "co2_seasonally_adjusted",
           "fit", "fit_seasonally_adjusted", "co2_filled", "co2_filled_seasonally_adjusted",
           "station"]
df = pd.read_csv(url, skiprows=64, names=columns, na_values=-99.99).dropna(subset=["co2"])

fig, ax = plt.subplots()
ax.plot(df["date"], df["co2"])
ax.set_xlabel("year")
ax.set_ylabel("CO2 (ppm)")
ax.set_title("Mauna Loa monthly CO2")

maidr.show(ax)   # opens the accessible chart in your web browser
~~~

When you run it, an accessible version of the line chart opens in your browser.

:::{admonition} If a browser doesn't open
:class: tip
On some setups `maidr.show(ax)` may not pop open a browser. If so, replace that last line
with `maidr.save_html(ax, file="co2.html")`, run the script again, then open the
`co2.html` file it creates in your browser.
:::

## How to explore the chart

Once the chart is open and has keyboard focus:

- **Arrow keys** — move left and right through the data points; you hear and/or read each
  one (its year and CO₂ value).
- **S** — toggle **Sonification** on and off. With it on, moving through the points plays a
  tone whose pitch rises and falls with the CO₂ value. Listen for the steady climb over the
  decades, with a small up-and-down wiggle each year (the seasonal cycle). That "shape" is
  what a sighted reader sees in the curve.
- **T** — toggle **Text** mode (a written description and per-point readout).
- **B** — toggle **Braille** mode (useful with a refreshable braille display).

The chart panel lists its own keyboard shortcuts, so if a key above behaves differently,
check that list (and tell us — it helps us write better instructions).

## What we'd love to know

- Could you navigate the chart and hear/read the values?
- Did the **sonification** give you a useful sense of the *shape* of the data — the rising
  trend, and the yearly cycle?
- Was anything confusing, broken, or hard to use?

This is one option we're evaluating. MAIDR currently handles common chart types — line, bar,
scatter, histogram, heatmap, boxplot — but **not yet** the 2D map-style plots (filled
contours, `quiver`/`streamplot`). So we'll likely pair it with plain-language descriptions
of the data throughout. Your experience here will tell us how much to lean on each.
