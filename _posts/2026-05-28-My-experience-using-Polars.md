---
title: 'My experience using Polars'
date: 2026-05-28
permalink: /posts/2026/05/polars-experience/
tags:
  - professional
  - finance
---

![Polars Icon](/images/polars_logo_icon_248809.png)

I was first exposed to `Polars` in my undergraduate data science class. My professor was a Canada Research Chair, so to him and the systems he built, speed was truly of the essence. Coming from a statistics and finance background, I understood how frustrating the wait for my `R` code or Excel VBA macro could be, but I was under the impression `Pandas` could solve all of that. `Pandas`, to put it bluntly, is written as the brainchild of a linear algebra expert, an amateur computer scientist, and a desperate analyst. Works and works well for its purpose, yet a bit clunky and difficult to dissect. 

I'm making this comparison because of my lived experience using both `Pandas` and `Polars` in a corporate environment. My current workplace is a bank, somewhere where the velocity of modern software slows down little by little, until it becomes a program where analysts take one look and decide "it works, so don't touch it". There is a data processing pipeline my team runs about 40 times a month, primarily within the span of one and a half weeks, which takes an average of 15-20 minutes each time. More than 10 hours a month are spent waiting, waiting, and waiting. Since it takes so long, usually a couple changes are made to our reports before it is run again. I fell victim to this time thief once, where my single mistake costed 40 minutes of the entire team's time, at 7 pm. I made the mistake, asked to run the process to update our reports, realized only when the process came back with results, then had to fix and ask to run it again. 40 minutes of wasted agony.

Enough was enough. I was already familiar with the speed and structural benefits of `Polars`, so I took it upon myself to rewrite the `Pandas`-centric pipeline in `Polars`. It took some convincing and business metrics to get started, but the project quickly ate up my working time. I promised my team speed benefits of at least 50%, but I had no idea if it could truly put a dent on the hours spent waiting. The first module I rewrote went from 8 minutes of processing to under 2 minutes. Promising. The more I wrote, the more these gains (or losses, depending on your outlook) scaled. By the end of the project, the 18 minute process was cut to 7 minutes. That's a full work day's worth of waiting which could be spent delivering reports faster, diving into deeper analysis, and connecting with stakeholders. Over a year, that is a full week's worth of time savings. There are additional time savings waiting to be captured, but that is another story entirely.

`Polars` aims to solve the issues `Pandas` has by learning from what made it so clunky. The code is written in an expression-based method which mirrors natural language, columns are strict, and steps are done lazily, among many other improvements. 

I learned how to code through a philosophical logic course in my second year of university. Didn't touch a computer terminal or type a single line of code, but the sequences of IFs, ANDs, and EXCEPTs built my coding expertise from first principles. So what a surprise it was to see `Pandas` be so finicky. A misplaced comma could make it all come crashing down. `Polars` makes the writing and explaining much easier. 

```python
# This is a snippet of Pandas code
df.loc["price" > 1, "volume"] = df["mean_volume"]
df[["price", "volume", "trader"]]
df.rename({"price":"notional"})

# This is the same snippet written in Polars.
df = (df
.with_columns(pl.when(pl.col("price") > 1)
                .then(pl.col("mean_volume"))
                .otherwise(pl.col("volume"))
                .alias("volume"))
.select(pl.col("price").alias("notional"), "volume", "trader"))
```

Notice the syntax, an untrained eye completely unfamiliar with `Polars` or `Pandas` can follow the clear wording in `Polars`, but may struggle with why some lines are written differently in `Pandas`. Fundamentally, `Polars` encourages clear notation using an expression-based approach. It reads like spoken language on purpose. 

Real life data is messy, unforgiving, and undeniably a pain to work through. This makes a strict data type per column extremely valuable. `Pandas` tries to run away from complexity by allowing different data to mix and mingle under an "object" column type, but `Polars` tackles it head on. `Polars` keeps your data consistent by enforcing strict data types and refusing to believe a string like "123" could possibly be the same as a numeric `123`. This creates a structure which standardizes all downstream processing, whether that continues to be in python or outsourced to Power BI and Excel. 

Finally, the lazy evaluation. I believe this is truly the magnum opus of `Polars`. The way I like to describe it to my curious coworkers is as follows: `Pandas` watches a cooking video like so, it receives an instruction, pauses the video, follows the instruction, drops what they are doing, then goes back to unpause the video and listen to the next step. That is like going to the store a separate time for each item on the ingredients list. Slow, clunky, awkward. `Polars` will instead watch the entire video and create a mental model of what to do, then execute when it decides it wants to cook. It is even capable of pushing instructions, like selecting a subset of columns earlier, if it can save time loading data. 

For the technically advanced, `Polars` is a bridge from `Pandas` to `Apache Spark`. For when your data is in the hundreds of thousands rather than the hundreds of millions. For my team, it is the perfect way for everyone to get in on the coding experience. I encourage all teams using Python and `Pandas` to start using `Polars`. It still has some catching up to do on applications like geostatistical modelling and some advanced functions, but the more users start to use it, the better it will become. 

![Polar bear > pandas](/images/polar_bear_panda.jpeg)

^ Bigger and better :)
