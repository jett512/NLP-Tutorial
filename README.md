# NLP-Tutorial
All steps are preformed in this notebook: Bikeshare cleaning 

 

The NLP Twitter cleaning process has 7 main sections. The number of sections may change based on the type of data. Here is a general overview of each step: 

Connect to google drive 

Combine all Files 

Find Original Tweets within 4 subsection quartiles 

Text cleaning 

Find Tweet ID’s outside of each quartile 

Translation 

Duplicate Removal 

 

For this example I will be using the Bikeshare twitter dataset. 

Below are the detailed steps to properly perform preprocessing steps for a twitter dataset, with some subsections for colab specific setup. 

 

Connect to google drive: 

I prefer to use google drive for saving and loading a majority of these files. The only exception is when I am utilizing a zipped folder, which I prefer to upload to colab directly. 

Combine all files: 

For this example, all of the files have been collected by year, leaving us with 13 individual files. To obtain the files into google collab, create a folder with all 13 files and zip it 

Once the files have been zipped, you can upload them to google collab, then unzip them to allow access to the folder within colab 

Using the glob library we can combine all files from a single folder 

Print the first 5 rows, and columns to double check that the data has been properly combined 

Save the combined file into google colab. 

Find original tweets: 

Now the next step will be to ensure that we are only using original tweets, not any retweets or quoted tweets. This step may not be needed for all NLP tasks, but this is needed for ours as we dont want any duplicate texts from the tweets. 

It is important to identify a source row, and a retweet row. These will be compared before and after this section to check if original tweets are kept, and retweets are removed. 

An important note is that we find the source tweets within 4 subsections of the full dataset. We could process this on the entire dataset, but colab’s RAM limitations could cause the program to crash, so we use 4 separate batches to avoid this. More batches may be needed for even larger datasets. 

After the retweets and quoted tweets have been removed, check our known tweets to see if the source was kept and the retweet removed. 

 

 

Text Cleaning: 

We will begin by creating an intermediate column called ‘text1’ from the ‘text’ column. This will be used to extract hashtags, mentions, links, and emojis. 

For all of the extraction steps except emojis, we will use the exact same method by using regex inside a lambda function to find specific symbols that correspond to each type of text we want to extract. Refer to the table below to see what regex expression is used 

 

Extractable 

Regex 

Symbol 

Mentions 

r'@(\w+)\s' 

@ 

Hashtags 

r'#(\w+)\s') 

# 

Links 

r'(https?://\S+)' 

https:// 

 

For emojis we will need to make use of the emoji library. I then created a function to look for the emoji unicode and then store it in its own column just like the other extractables.  

Now that all of the extractables have been stored in their own column. I remove all of them from the text, along with punctuation and extra spaces. This cleaned text will then be stored in the new ‘text_clean’ column 

The final step in the text cleaning is to remove specific symbols that weren't caught in the punctuation removal and convert all text to lower case. These symbols are removed using regex as well, and I found which symbols and characters should be removed by investigating the dataset and seeing what was missed by the previous functions. 

Find Tweet ID’s outside of each quartile 

Here we use the full dataset to search for missing ‘text1’ rows and match them with their corresponding source tweet_id, then replace them as needed just like in previous steps.  

Once we have found all rows that were missing their corresponding source tweet, we then apply the same cleaning to them to make sure that they are also cleaned. 

Translation 

For translation we make use of the google translate package. This will allow us to translate all rows automatically using the google translate API. 

Before we translate, it is important to identify a row that is not in english, so that after we can check if the translation worked. 

To start, we identify all rows with the ‘lang’ column != to en. This means that the language of the tweet is not english, so we must translate these columns. All columns that are identified as english are not altered in this step.  

Using the non-english rows, we will use the API to translate them and store the original language in a new column called ‘original_language 

Now we will use the row we identified as not english to check if the translation worked. If it did, then the row would have been translated to english. Although a limitation of google translate is that it doesn't always translate some slang words, so keep this in mind. 

Duplicate Removal 

Even if some tweets are retweets, they may be duplicate or spam tweets. These must be removed to ensure that they aren't being over represented. 

To start, we assign a unique verification id to each row, to track duplicate removal. 

Then we make use of the .drop_duplicates function from pandas to remove duplicate text. This should remove all identical text from the ‘text_clean’ column. Although keep in mind any extra characters of spaces can cause the text to not be identified as not duplicated. 

 
