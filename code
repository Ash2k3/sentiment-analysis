import tweepy
from langdetect import detect
from transformers import pipeline
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
import streamlit as st

# Twitter API credentials
consumer_key = 'YOUR_CONSUMER_KEY'
consumer_secret = 'YOUR_CONSUMER_SECRET'
access_token = 'YOUR_ACCESS_TOKEN'
access_token_secret = 'YOUR_ACCESS_TOKEN_SECRET'

# Authenticate to Twitter
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth)

# Load sentiment analysis model
sentiment_pipeline = pipeline("sentiment-analysis")

nltk.download("punkt")
nltk.download("stopwords")


def preprocess(text):
    stop_words = set(stopwords.words("english"))
    word_tokens = word_tokenize(text)
    filtered_text = [word for word in word_tokens if word.lower() not in stop_words]
    return " ".join(filtered_text)


class MyStreamListener(tweepy.StreamingClient):
    def on_tweet(self, tweet):
        text = tweet.text
        try:
            lang = detect(text)
            preprocessed_text = preprocess(text)
            sentiment = sentiment_pipeline(preprocessed_text)

            # Display results in Streamlit
            st.write(f"**Tweet:** {text}")
            st.write(f"**Language:** {lang}")
            st.write(f"**Sentiment:** {sentiment[0]['label']} (Score: {sentiment[0]['score']:.2f})")
        except Exception as e:
            st.write("Error processing tweet: ", e)


def start_streaming(keywords):
    myStreamListener = MyStreamListener(bearer_token='YOUR_BEARER_TOKEN')

    st.write("Starting stream...")

    # Filtering the stream for specific keywords
    myStreamListener.add_rules(tweepy.StreamRule(" OR ".join(keywords)))
    myStreamListener.filter()
    

# Streamlit UI
st.title("Real-Time Sentiment Analysis Tool")
keywords_input = st.text_input("Enter keywords to track (comma-separated)", "Python, AI")

if st.button("Start Stream"):
    keywords = [kw.strip() for kw in keywords_input.split(",")]
    start_streaming(keywords)
