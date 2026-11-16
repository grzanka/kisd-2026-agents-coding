# Rules for this project

Weather data of the GIG station in Katowice, 2006-2026, as one table in
data/meteo.parquet (one row per half hour, column datetime plus the station's
own column names).

- Before each tool call, say in one sentence what you will do and why.
- Run Python only with .venv\Scripts\python.exe (on Linux and macOS:
  .venv/bin/python). Do not install packages unless I ask for it.
- Never change data/meteo.parquet. Write results only into results/.
- Never open a plot window; save plots as PNG files.
- Do not read the data into the conversation. Write code that summarises it.
- Data source: meteo.gig.eu archive, GIG Katowice, licence CC BY 4.0. Cite it
  in every report and in the caption of every plot.
