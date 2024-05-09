# ds334_final_project

My final project is on the "sticky stuff" scandal in Major League Baseball in 2021, in which pitchers were abusing sticky substances to get higher spin rates. With the higher spin rates, less batters were getting hits and thus attendance began to drop. Through a shiny app I am interested in looking at just how much the spin rate impacted batting statistics as well as game attendance and TV viewership.

```{r message=FALSE, warning=FALSE, echo=FALSE}
library(here)
library(readr)
library(tidyverse)
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
pitching_df <- read_csv(here("data/pitchingstats.csv"))
attendance_2018 <- read_csv(here("data/2018attendance.csv"))
attendance_2019 <- read_csv(here("data/2019attendance.csv"))
attendance_2020 <- read_csv(here("data/2020attendance.csv"))
attendance_2021 <- read_csv(here("data/2021attendance.csv"))
attendance_2022 <- read_csv(here("data/2022attendance.csv"))
attendance_2023 <- read_csv(here("data/2023attendance.csv"))
attendance_2024 <- read_csv(here("data/2024attendance.csv"))
worldseriesviewing <- read_csv(here("data/world-series-ratings.csv"))
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
attendance_2018$year <- 2018
attendance_2019$year <- 2019
attendance_2020$year <- 2020
attendance_2021$year <- 2021
attendance_2022$year <- 2022
attendance_2023$year <- 2023
attendance_2024$year <- 2024
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
attendance2018_2019 <- full_join(attendance_2018, attendance_2019)
attendance2020_2021 <- full_join(attendance_2020, attendance_2021)
attendance2022_2023 <- full_join(attendance_2022, attendance_2023)
attendance2018_2021 <- full_join(attendance2018_2019, attendance2020_2021)
attendance2022_2024 <- full_join(attendance2022_2023, attendance_2024)
attendancefull <- full_join(attendance2018_2021, attendance2022_2024)
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
pitching_df <- pitching_df |> rename(pitcher = `last_name, first_name`)
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
pitching_cleaner <- pitching_df |> pivot_longer(c("sl_avg_spin", "ch_avg_spin", "cu_avg_spin", "si_avg_spin"), names_to = "pitch_type", values_to =  "spin_rate")

pitching_cleaner <- pitching_cleaner |> mutate(pitch_type = str_remove(pitch_type, "_avg_spin")) |>  mutate(pitch_type = case_when(pitch_type == "sl" ~ "slider", pitch_type == "ch" ~ "changeup", pitch_type == "cu" ~ "curveball", pitch_type == "si" ~ "sinker"))  

pitching_cleaner <- pitching_cleaner |> select(!c(sl_avg_speed, ch_avg_speed, si_avg_speed, cu_avg_speed, pa, player_id)) 
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
attendance_smaller <- attendancefull |> select(Tm, Attendance, `Attend/G`, year)
```

```{r message=FALSE, warning=FALSE, echo=FALSE}
tv_views_smaller <- worldseriesviewing |> select(c(year, average_audience)) |> filter(year >= 2018 & year <= 2023)
```

```{r}
ggplot(data = attendance_smaller, aes(x = year, y = `Attend/G`, colour = Tm)) + geom_line()
```

```{r}
ggplot(data = pitching_cleaner, aes(x = spin_rate)) + geom_histogram() + facet_wrap(~year) + theme_minimal() + labs(title = "Histogram of spin rate facted by year")
```
