# An Overview of Japanese Natural Language Processing Tools through Unsupervised Aspect-Based Sentiment Analysis of Hotel Reviews

Tan Wei Chiong

---

## ***READ THIS BEFORE RUNNING `pip install requirements.txt`***<br>
MeCab is a widely-used dictionary for tokenizing Japanese text, written in C++. Almost all of the libraries we use for working with Japanese text are / use Python wrappers for MeCab, and thus would require the installation of wheels so that the tools know where to find the dictionary when called.

Usually this wouldn't be a problem, as `pip` or `conda` would install everything under the hood. However, MeCab seems to not behave too well in that regard, and installation of some libraries may fail as a result. This isn't an issue with `pip`, and has to be worked around outside of `pip` / `conda`. 

### Fixes
Use one of the two fixes below before running `pip install requirements.txt`:
1. Download a build tool for C++ ([Microsoft C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) would do the trick), so that `pip` can construct the wheels for MeCab as needed.

2. Download and install MeCab from https://github.com/ikegami-yukino/mecab/releases. Specifically, one should download `mecab-64-0.996.2.exe`, and when prompted, set the encoding to UTF-8. You may have to set the PATH manually to wherever the MeCab installation is, if the following code below does not do the trick:
```python
import os
# this explicitly tells python where to find the MeCab dictionary
# replace with the path to the libmecab.dll if you get an error running the MeCab() instantiation below
os.environ['MECAB_PATH']="C:/Program Files/MeCab/bin/libmecab.dll"
# we need to force MeCab to read Japanese text in UTF-8
os.environ['MECAB_CHARSET']='utf-8'
```
If this does not work, try manually setting `MECAB_PATH` in your environment variables to the location where `libmecab.dll` is, and set `MECAB_CHARSET` to `utf-8`.

---

## Introduction

This project started off as a capstone submission for General Assembly's Data Science Immersive course. It is meant more as a proof of concept that it is possible to work directly with Japanese text in NLP, rather than having to translate it to English, and then using the usual NLP tools to perform an analysis. This also serves to document a few of the Japanese tools for which there isn't much English documentation of.

As such, the bulk of the project lies in exploring the different Japanese NLP tools available for the task at hand, and therefore would not flow like a "standard" data science project, where one would typically craft a problem statement and stick to it throughout. Here, the problem statement acts more like a canvas of sorts, to allow us to explore how Japanese NLP tools would work differently from the usual English-language NLP tools, such as NLTK and VADER.

This also means that there would not be much content in terms of model construction and prediction, as most of the work would be done by pre-trained sentiment analyzers (specifically [oseti](https://github.com/ikegami-yukino/oseti) and [asari](https://github.com/Hironsan/asari)). Given more time, more would have been done, but this is rather substantial as is with learning the completely new tools at hand, and integrating them into the Scikit-Learn tools that we are so used to.

## Project Structure

This project spans five notebooks, which can be grouped into two broad categories.

### Scraping Notebooks (00 and 01)

The notebook `00_exploratory_scraping.ipynb`, as its name implies, serves as an exploration of the Rakuten Travel website, and at the end, a pseudocode structure for the scraping of the desired data is constructed.

From there, the notebook `01_actual_scraping.ipynb` deals with the actual scraping of the data. Around 7340 individual hotel reviews from hotels in the Tohoku region were scraped, some with replies by the hotel. This takes around 4 hours to run. At the end of the notebook, a dataset `rakuten_tohoku_reviews_replies.csv` is exported, which will be used in the notebooks following.

### EDA Notebooks (02, 03, 04)

Exploratory Data Analysis starts off with the `02_exploratory_data_analysis_part_1.ipynb` notebook. This does an initial cleaning and exploration of the reviews data. It also includes most of the feature engineering, and introduces the main tokenizers and sentiment analyzers that are used for working with Japanese text data. This notebook also addresses how Japanese tokenizers, which have an output that is different from the familiar English tokenizers, can be coerced to work with Scikit-Learn's vectorizers (the key insight is that a tokenizer is simply a function that takes in a document and outputs a list of tokens, whatever that may be). The notebook outputs a dataset `rakuten_tohoku_reviews_sentiments.csv`.

The `03_exploratory_data_analysis_part_2.ipynb` notebook mainly deals with comparing the performance of two Japanese sentiment analyzers `oseti` and `asari`. In order to do so, sentiment predictions from `rakuten_tohoku_reviews_sentiments.csv` are converted into integer ratings from 1 to 5, and the mean absolute errors of the predicted ratings from both sentiment analyzers are compared across all aspects. For the intents and purposes of this project, this would be the final notebook.

Lastly, the `04_exploratory_data_analysis_part_3.ipynb` notebook is more experimental, where `deep-translator`'s `GoogleTranslator` is used to translate the Japanese reviews into English, and the sentiments analyzed by VADER. An initial scan of the results shows that Google Translate actually captures the sentiment of the Japanese text extremely well - a testament to the capabilities of Google Translate. However, as the translator takes around 11 seconds to translate a single sentence (not even a single review!), this part of the project has been pushed to future work, and a proper analysis would have to wait until then.

## Others

This project also comes with slides and an appendix which qualitatively details much of the workings of the Japanese NLP tools used, with minimal interruptions of code. If one simply wants an overview of the available tools without having to look through all the notebooks, the appendix is packaged nicely as a `.pdf`. For the sake of completeness, the `.tex` files for both the appendix and slides will also be included.

## Acknowledgements

I would like to thank General Assembly's class of SG-DSI-41, as without them, this project would not have been possible. Special mention goes to the instructor Waseem Sheriff, and the instructional associate Rustam Shariq. They (and the rest of the class) have been wonderful collaborators throughout the 3 months we spent together, and have been indispensible in the journey that led up to this project.

## References

1. Yuki Nakayama, Koji Murakami, Gautam Kumar, Sudha Bhingardive, and Ikuko Hardaway. A large-scale Japanese dataset for aspect-based sentiment analysis. In Nicoletta Calzolari, Frédéric Béchet, Philippe Blache, Khalid Choukri, Christopher Cieri, Thierry Declerck, Sara Goggi, Hitoshi Isahara, Bente Maegaard, Joseph Mariani, Hélène Mazo, Jan Odijk, and Stelios Piperidis, editors, Proceedings of the Thirteenth Language Resources and Evaluation Conference, pages 7014–7021, Marseille, France, 06 2022. European Language Resources Association

