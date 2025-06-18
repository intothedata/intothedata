+++
title = "소셜미디어 데이터 수집"
type = "docs"
weight = 1035
+++

## 소셜미디어 데이터 수집

### 개요

소셜미디어 플랫폼은 사용자들의 실시간 의견, 감정, 트렌드 정보를 담고 있는 거대한 데이터 저장소이다. 이러한 데이터는 브랜드 모니터링, 감정 분석, 트렌드 예측, 마케팅 전략 수립에 중요한 인사이트를 제공한다.

### 소셜미디어 데이터의 특성

#### 비구조화된 데이터
- 텍스트, 이미지, 동영상 등 다양한 형태
- 줄임말, 이모지, 해시태그 등 독특한 표현
- 맥락에 따라 의미가 달라지는 언어적 특성

#### 실시간성
- 실시간으로 생성되는 대량의 데이터
- 바이럴 콘텐츠의 급속한 확산
- 이벤트와 연관된 급격한 트래픽 변화

#### 네트워크 효과
- 사용자 간의 상호작용 (좋아요, 댓글, 공유)
- 인플루언서의 영향력
- 커뮤니티 내에서의 정보 전파

### 주요 플랫폼별 데이터 수집

#### Twitter API v2
```python
import tweepy
import pandas as pd
from datetime import datetime, timedelta
import json

class TwitterDataCollector:
    def __init__(self, bearer_token):
        self.client = tweepy.Client(bearer_token=bearer_token)
        
    def search_tweets(self, query, max_results=100, start_time=None, end_time=None):
        """
        트윗 검색 및 수집
        """
        try:
            tweets = self.client.search_recent_tweets(
                query=query,
                max_results=max_results,
                start_time=start_time,
                end_time=end_time,
                tweet_fields=['created_at', 'author_id', 'public_metrics', 'context_annotations', 'lang'],
                user_fields=['username', 'public_metrics', 'verified'],
                expansions=['author_id']
            )
            
            # 데이터 정리
            tweet_data = []
            users_dict = {}
            
            # 사용자 정보 딕셔너리 생성
            if tweets.includes and 'users' in tweets.includes:
                for user in tweets.includes['users']:
                    users_dict[user.id] = user
            
            # 트윗 데이터 처리
            if tweets.data:
                for tweet in tweets.data:
                    user = users_dict.get(tweet.author_id)
                    
                    tweet_info = {
                        'tweet_id': tweet.id,
                        'text': tweet.text,
                        'created_at': tweet.created_at,
                        'author_id': tweet.author_id,
                        'username': user.username if user else None,
                        'user_verified': user.verified if user else None,
                        'user_followers': user.public_metrics['followers_count'] if user else 0,
                        'retweet_count': tweet.public_metrics['retweet_count'],
                        'like_count': tweet.public_metrics['like_count'],
                        'reply_count': tweet.public_metrics['reply_count'],
                        'quote_count': tweet.public_metrics['quote_count'],
                        'lang': tweet.lang,
                        'context_annotations': tweet.context_annotations
                    }
                    tweet_data.append(tweet_info)
            
            return pd.DataFrame(tweet_data)
            
        except Exception as e:
            print(f"트위터 데이터 수집 오류: {e}")
            return pd.DataFrame()
    
    def collect_hashtag_data(self, hashtag, days=7):
        """
        특정 해시태그의 시계열 데이터 수집
        """
        end_time = datetime.now()
        start_time = end_time - timedelta(days=days)
        
        query = f"#{hashtag} -is:retweet lang:ko"
        
        all_tweets = []
        
        # 페이지네이션 처리
        for i in range(0, days):
            day_start = start_time + timedelta(days=i)
            day_end = day_start + timedelta(days=1)
            
            daily_tweets = self.search_tweets(
                query=query,
                max_results=100,
                start_time=day_start,
                end_time=day_end
            )
            
            if not daily_tweets.empty:
                all_tweets.append(daily_tweets)
        
        if all_tweets:
            return pd.concat(all_tweets, ignore_index=True)
        else:
            return pd.DataFrame()
    
    def get_user_timeline(self, username, max_results=100):
        """
        특정 사용자의 타임라인 수집
        """
        try:
            user = self.client.get_user(username=username)
            tweets = self.client.get_users_tweets(
                id=user.data.id,
                max_results=max_results,
                tweet_fields=['created_at', 'public_metrics', 'context_annotations']
            )
            
            timeline_data = []
            if tweets.data:
                for tweet in tweets.data:
                    timeline_data.append({
                        'tweet_id': tweet.id,
                        'text': tweet.text,
                        'created_at': tweet.created_at,
                        'retweet_count': tweet.public_metrics['retweet_count'],
                        'like_count': tweet.public_metrics['like_count'],
                        'reply_count': tweet.public_metrics['reply_count']
                    })
            
            return pd.DataFrame(timeline_data)
            
        except Exception as e:
            print(f"사용자 타임라인 수집 오류: {e}")
            return pd.DataFrame()

# 사용 예시
collector = TwitterDataCollector("YOUR_BEARER_TOKEN")

# 특정 키워드 검색
covid_tweets = collector.search_tweets("코로나19 백신", max_results=100)

# 해시태그 트렌드 분석
hashtag_data = collector.collect_hashtag_data("데이터사이언스", days=30)
```

#### Instagram API (Instagram Basic Display API)
```python
import requests
import json
from datetime import datetime

class InstagramDataCollector:
    def __init__(self, access_token):
        self.access_token = access_token
        self.base_url = "https://graph.instagram.com"
    
    def get_user_media(self, user_id="me", limit=25):
        """
        사용자의 미디어 게시물 수집
        """
        url = f"{self.base_url}/{user_id}/media"
        params = {
            'fields': 'id,caption,media_type,media_url,permalink,timestamp,like_count,comments_count',
            'access_token': self.access_token,
            'limit': limit
        }
        
        try:
            response = requests.get(url, params=params)
            response.raise_for_status()
            
            data = response.json()
            media_data = []
            
            for item in data.get('data', []):
                media_info = {
                    'id': item.get('id'),
                    'caption': item.get('caption', ''),
                    'media_type': item.get('media_type'),
                    'media_url': item.get('media_url'),
                    'permalink': item.get('permalink'),
                    'timestamp': item.get('timestamp'),
                    'like_count': item.get('like_count', 0),
                    'comments_count': item.get('comments_count', 0)
                }
                media_data.append(media_info)
            
            return pd.DataFrame(media_data)
            
        except Exception as e:
            print(f"인스타그램 데이터 수집 오류: {e}")
            return pd.DataFrame()
    
    def get_media_comments(self, media_id):
        """
        특정 게시물의 댓글 수집
        """
        url = f"{self.base_url}/{media_id}/comments"
        params = {
            'fields': 'id,text,timestamp,username',
            'access_token': self.access_token
        }
        
        try:
            response = requests.get(url, params=params)
            response.raise_for_status()
            
            data = response.json()
            comments = []
            
            for comment in data.get('data', []):
                comments.append({
                    'comment_id': comment.get('id'),
                    'text': comment.get('text'),
                    'timestamp': comment.get('timestamp'),
                    'username': comment.get('username'),
                    'media_id': media_id
                })
            
            return pd.DataFrame(comments)
            
        except Exception as e:
            print(f"댓글 수집 오류: {e}")
            return pd.DataFrame()
```

#### YouTube Data API
```python
from googleapiclient.discovery import build
import pandas as pd

class YouTubeDataCollector:
    def __init__(self, api_key):
        self.youtube = build('youtube', 'v3', developerKey=api_key)
    
    def search_videos(self, query, max_results=50, order='relevance'):
        """
        동영상 검색
        """
        try:
            search_response = self.youtube.search().list(
                q=query,
                part='snippet',
                maxResults=max_results,
                order=order,
                type='video'
            ).execute()
            
            videos = []
            for item in search_response['items']:
                video_info = {
                    'video_id': item['id']['videoId'],
                    'title': item['snippet']['title'],
                    'description': item['snippet']['description'],
                    'channel_id': item['snippet']['channelId'],
                    'channel_title': item['snippet']['channelTitle'],
                    'published_at': item['snippet']['publishedAt'],
                    'thumbnail_url': item['snippet']['thumbnails']['high']['url']
                }
                videos.append(video_info)
            
            return pd.DataFrame(videos)
            
        except Exception as e:
            print(f"YouTube 검색 오류: {e}")
            return pd.DataFrame()
    
    def get_video_statistics(self, video_ids):
        """
        동영상 통계 정보 수집
        """
        try:
            # 한 번에 최대 50개까지 요청 가능
            video_ids_str = ','.join(video_ids[:50])
            
            stats_response = self.youtube.videos().list(
                part='statistics,snippet',
                id=video_ids_str
            ).execute()
            
            stats = []
            for item in stats_response['items']:
                video_stats = {
                    'video_id': item['id'],
                    'view_count': int(item['statistics'].get('viewCount', 0)),
                    'like_count': int(item['statistics'].get('likeCount', 0)),
                    'comment_count': int(item['statistics'].get('commentCount', 0)),
                    'duration': item['snippet'].get('duration'),
                    'category_id': item['snippet'].get('categoryId')
                }
                stats.append(video_stats)
            
            return pd.DataFrame(stats)
            
        except Exception as e:
            print(f"통계 수집 오류: {e}")
            return pd.DataFrame()
    
    def get_video_comments(self, video_id, max_results=100):
        """
        동영상 댓글 수집
        """
        try:
            comments_response = self.youtube.commentThreads().list(
                part='snippet',
                videoId=video_id,
                maxResults=max_results,
                order='time'
            ).execute()
            
            comments = []
            for item in comments_response['items']:
                comment = item['snippet']['topLevelComment']['snippet']
                comments.append({
                    'comment_id': item['id'],
                    'video_id': video_id,
                    'text': comment['textDisplay'],
                    'author': comment['authorDisplayName'],
                    'like_count': comment['likeCount'],
                    'published_at': comment['publishedAt']
                })
            
            return pd.DataFrame(comments)
            
        except Exception as e:
            print(f"댓글 수집 오류: {e}")
            return pd.DataFrame()

# 사용 예시
youtube_collector = YouTubeDataCollector("YOUR_API_KEY")

# 동영상 검색
videos = youtube_collector.search_videos("데이터사이언스", max_results=50)

# 통계 정보 수집
if not videos.empty:
    video_ids = videos['video_id'].tolist()
    stats = youtube_collector.get_video_statistics(video_ids)
    
    # 데이터 병합
    final_data = videos.merge(stats, on='video_id', how='left')
```

#### Facebook Graph API
```python
import facebook
import pandas as pd

class FacebookDataCollector:
    def __init__(self, access_token):
        self.graph = facebook.GraphAPI(access_token=access_token)
    
    def get_page_posts(self, page_id, limit=100):
        """
        페이지 게시물 수집
        """
        try:
            posts = self.graph.get_connections(
                page_id, 
                'posts',
                fields='id,message,created_time,likes.summary(true),comments.summary(true),shares',
                limit=limit
            )
            
            post_data = []
            for post in posts['data']:
                post_info = {
                    'post_id': post.get('id'),
                    'message': post.get('message', ''),
                    'created_time': post.get('created_time'),
                    'likes_count': post.get('likes', {}).get('summary', {}).get('total_count', 0),
                    'comments_count': post.get('comments', {}).get('summary', {}).get('total_count', 0),
                    'shares_count': post.get('shares', {}).get('count', 0)
                }
                post_data.append(post_info)
            
            return pd.DataFrame(post_data)
            
        except Exception as e:
            print(f"Facebook 게시물 수집 오류: {e}")
            return pd.DataFrame()
    
    def get_post_comments(self, post_id, limit=100):
        """
        게시물 댓글 수집
        """
        try:
            comments = self.graph.get_connections(
                post_id,
                'comments',
                fields='id,message,created_time,from,like_count',
                limit=limit
            )
            
            comment_data = []
            for comment in comments['data']:
                comment_info = {
                    'comment_id': comment.get('id'),
                    'post_id': post_id,
                    'message': comment.get('message'),
                    'created_time': comment.get('created_time'),
                    'author_name': comment.get('from', {}).get('name'),
                    'author_id': comment.get('from', {}).get('id'),
                    'like_count': comment.get('like_count', 0)
                }
                comment_data.append(comment_info)
            
            return pd.DataFrame(comment_data)
            
        except Exception as e:
            print(f"댓글 수집 오류: {e}")
            return pd.DataFrame()
```

### 웹 스크래핑을 통한 수집

#### Reddit 데이터 수집
```python
import praw
import pandas as pd
from datetime import datetime

class RedditDataCollector:
    def __init__(self, client_id, client_secret, user_agent):
        self.reddit = praw.Reddit(
            client_id=client_id,
            client_secret=client_secret,
            user_agent=user_agent
        )
    
    def get_subreddit_posts(self, subreddit_name, sort='hot', limit=100):
        """
        서브레딧 게시물 수집
        """
        try:
            subreddit = self.reddit.subreddit(subreddit_name)
            
            if sort == 'hot':
                posts = subreddit.hot(limit=limit)
            elif sort == 'new':
                posts = subreddit.new(limit=limit)
            elif sort == 'top':
                posts = subreddit.top(limit=limit)
            else:
                posts = subreddit.hot(limit=limit)
            
            post_data = []
            for post in posts:
                post_info = {
                    'post_id': post.id,
                    'title': post.title,
                    'selftext': post.selftext,
                    'author': str(post.author),
                    'created_utc': datetime.fromtimestamp(post.created_utc),
                    'score': post.score,
                    'upvote_ratio': post.upvote_ratio,
                    'num_comments': post.num_comments,
                    'url': post.url,
                    'subreddit': post.subreddit.display_name,
                    'is_video': post.is_video,
                    'over_18': post.over_18
                }
                post_data.append(post_info)
            
            return pd.DataFrame(post_data)
            
        except Exception as e:
            print(f"Reddit 데이터 수집 오류: {e}")
            return pd.DataFrame()
    
    def get_post_comments(self, post_id, limit=100):
        """
        게시물 댓글 수집
        """
        try:
            submission = self.reddit.submission(id=post_id)
            submission.comments.replace_more(limit=0)
            
            comments = []
            for comment in submission.comments.list()[:limit]:
                if hasattr(comment, 'body'):
                    comment_info = {
                        'comment_id': comment.id,
                        'post_id': post_id,
                        'body': comment.body,
                        'author': str(comment.author),
                        'created_utc': datetime.fromtimestamp(comment.created_utc),
                        'score': comment.score,
                        'parent_id': comment.parent_id
                    }
                    comments.append(comment_info)
            
            return pd.DataFrame(comments)
            
        except Exception as e:
            print(f"댓글 수집 오류: {e}")
            return pd.DataFrame()
```

#### 네이버 블로그/카페 데이터 수집
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import time
import re

class NaverDataCollector:
    def __init__(self):
        self.session = requests.Session()
        self.session.headers.update({
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
        })
    
    def search_blog_posts(self, query, start=1, display=10):
        """
        네이버 블로그 검색 결과 수집
        """
        try:
            url = "https://openapi.naver.com/v1/search/blog.json"
            headers = {
                'X-Naver-Client-Id': 'YOUR_CLIENT_ID',
                'X-Naver-Client-Secret': 'YOUR_CLIENT_SECRET'
            }
            
            params = {
                'query': query,
                'start': start,
                'display': display,
                'sort': 'date'
            }
            
            response = requests.get(url, headers=headers, params=params)
            response.raise_for_status()
            
            data = response.json()
            
            blog_posts = []
            for item in data.get('items', []):
                post_info = {
                    'title': self.clean_html(item.get('title', '')),
                    'description': self.clean_html(item.get('description', '')),
                    'blogger_name': item.get('bloggername', ''),
                    'blogger_link': item.get('bloggerlink', ''),
                    'post_date': item.get('postdate', ''),
                    'link': item.get('link', '')
                }
                blog_posts.append(post_info)
            
            return pd.DataFrame(blog_posts)
            
        except Exception as e:
            print(f"네이버 블로그 검색 오류: {e}")
            return pd.DataFrame()
    
    def clean_html(self, text):
        """
        HTML 태그 제거
        """
        clean = re.compile('<.*?>')
        return re.sub(clean, '', text)
    
    def get_cafe_posts(self, cafe_id, board_id, page=1):
        """
        네이버 카페 게시물 수집 (웹 스크래핑)
        """
        try:
            url = f"https://cafe.naver.com/ArticleList.nhn"
            params = {
                'search.clubid': cafe_id,
                'search.boardtype': 'L',
                'search.menuid': board_id,
                'search.page': page
            }
            
            response = self.session.get(url, params=params)
            soup = BeautifulSoup(response.content, 'html.parser')
            
            posts = []
            article_list = soup.find('div', class_='article-board')
            
            if article_list:
                for article in article_list.find_all('tr'):
                    title_elem = article.find('a', class_='article')
                    if title_elem:
                        post_info = {
                            'title': title_elem.get_text(strip=True),
                            'link': title_elem.get('href', ''),
                            'author': '',  # 추가 파싱 필요
                            'date': '',    # 추가 파싱 필요
                            'views': ''    # 추가 파싱 필요
                        }
                        posts.append(post_info)
            
            return pd.DataFrame(posts)
            
        except Exception as e:
            print(f"네이버 카페 수집 오류: {e}")
            return pd.DataFrame()
```

### 데이터 전처리 및 정제

#### 텍스트 전처리
```python
import re
import emoji
from konlpy.tag import Okt
from textblob import TextBlob

class SocialMediaTextPreprocessor:
    def __init__(self):
        self.okt = Okt()
        
    def clean_text(self, text):
        """
        텍스트 정제
        """
        if not isinstance(text, str):
            return ""
        
        # 이모지 처리
        text = emoji.demojize(text, language='ko')
        
        # URL 제거
        text = re.sub(r'http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*\\(\\),]|(?:%[0-9a-fA-F][0-9a-fA-F]))+', '', text)
        
        # 멘션 제거 (@username)
        text = re.sub(r'@[A-Za-z0-9_]+', '', text)
        
        # 해시태그에서 # 제거 (단어는 유지)
        text = re.sub(r'#([A-Za-z0-9_가-힣]+)', r'\1', text)
        
        # 특수문자 제거 (한글, 영문, 숫자, 공백만 유지)
        text = re.sub(r'[^가-힣a-zA-Z0-9\s]', '', text)
        
        # 연속된 공백 제거
        text = re.sub(r'\s+', ' ', text)
        
        return text.strip()
    
    def extract_hashtags(self, text):
        """
        해시태그 추출
        """
        hashtags = re.findall(r'#([A-Za-z0-9_가-힣]+)', text)
        return hashtags
    
    def extract_mentions(self, text):
        """
        멘션 추출
        """
        mentions = re.findall(r'@([A-Za-z0-9_]+)', text)
        return mentions
    
    def tokenize_korean(self, text):
        """
        한글 형태소 분석
        """
        try:
            tokens = self.okt.morphs(text, stem=True)
            return tokens
        except:
            return []
    
    def get_sentiment_score(self, text):
        """
        감정 점수 계산 (영문 텍스트)
        """
        try:
            blob = TextBlob(text)
            return blob.sentiment.polarity
        except:
            return 0.0
    
    def preprocess_dataframe(self, df, text_column):
        """
        데이터프레임 전체 전처리
        """
        # 텍스트 정제
        df['cleaned_text'] = df[text_column].apply(self.clean_text)
        
        # 해시태그 추출
        df['hashtags'] = df[text_column].apply(self.extract_hashtags)
        
        # 멘션 추출
        df['mentions'] = df[text_column].apply(self.extract_mentions)
        
        # 토큰화
        df['tokens'] = df['cleaned_text'].apply(self.tokenize_korean)
        
        # 감정 점수
        df['sentiment_score'] = df['cleaned_text'].apply(self.get_sentiment_score)
        
        return df

# 사용 예시
preprocessor = SocialMediaTextPreprocessor()
processed_tweets = preprocessor.preprocess_dataframe(tweet_data, 'text')
```

### 실시간 모니터링 시스템

#### 키워드 알림 시스템
```python
import time
import smtplib
from email.mime.text import MIMEText
from datetime import datetime, timedelta

class SocialMediaMonitor:
    def __init__(self, twitter_collector, email_config):
        self.twitter_collector = twitter_collector
        self.email_config = email_config
        self.monitored_keywords = []
        self.alert_history = []
    
    def add_keyword_monitor(self, keyword, alert_threshold=10):
        """
        모니터링할 키워드 추가
        """
        self.monitored_keywords.append({
            'keyword': keyword,
            'threshold': alert_threshold,
            'last_check': datetime.now()
        })
    
    def check_keyword_trends(self):
        """
        키워드 트렌드 확인
        """
        for keyword_info in self.monitored_keywords:
            keyword = keyword_info['keyword']
            threshold = keyword_info['threshold']
            last_check = keyword_info['last_check']
            
            # 지난 1시간 데이터 수집
            recent_tweets = self.twitter_collector.search_tweets(
                query=keyword,
                max_results=100,
                start_time=last_check
            )
            
            if len(recent_tweets) >= threshold:
                self.send_alert(keyword, len(recent_tweets))
            
            keyword_info['last_check'] = datetime.now()
    
    def send_alert(self, keyword, tweet_count):
        """
        알림 이메일 발송
        """
        try:
            subject = f"소셜미디어 트렌드 알림: {keyword}"
            body = f"""
            키워드 '{keyword}'에 대한 급증 트렌드가 감지되었습니다.
            
            지난 1시간 동안 {tweet_count}개의 새로운 트윗이 발견되었습니다.
            
            시간: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}
            """
            
            msg = MIMEText(body)
            msg['Subject'] = subject
            msg['From'] = self.email_config['sender']
            msg['To'] = self.email_config['recipient']
            
            server = smtplib.SMTP(self.email_config['smtp_server'], self.email_config['smtp_port'])
            server.starttls()
            server.login(self.email_config['username'], self.email_config['password'])
            server.send_message(msg)
            server.quit()
            
            print(f"알림 발송 완료: {keyword}")
            
        except Exception as e:
            print(f"알림 발송 실패: {e}")
    
    def start_monitoring(self, check_interval=3600):
        """
        모니터링 시작 (1시간마다 체크)
        """
        print("소셜미디어 모니터링 시작...")
        
        while True:
            try:
                self.check_keyword_trends()
                time.sleep(check_interval)
            except KeyboardInterrupt:
                print("모니터링 중단")
                break
            except Exception as e:
                print(f"모니터링 오류: {e}")
                time.sleep(60)  # 오류 시 1분 후 재시도

# 사용 예시
email_config = {
    'smtp_server': 'smtp.gmail.com',
    'smtp_port': 587,
    'username': 'your_email@gmail.com',
    'password': 'your_password',
    'sender': 'your_email@gmail.com',
    'recipient': 'recipient@gmail.com'
}

monitor = SocialMediaMonitor(twitter_collector, email_config)
monitor.add_keyword_monitor("데이터 유출", alert_threshold=20)
monitor.add_keyword_monitor("개인정보", alert_threshold=15)
monitor.start_monitoring()
```

### 데이터 분석 및 시각화

#### 트렌드 분석
```python
import matplotlib.pyplot as plt
import seaborn as sns
from collections import Counter
import networkx as nx

class SocialMediaAnalyzer:
    def __init__(self, data):
        self.data = data
        
    def analyze_temporal_trends(self, date_column, value_column):
        """
        시간별 트렌드 분석
        """
        # 날짜별 집계
        daily_counts = self.data.groupby(
            self.data[date_column].dt.date
        )[value_column].count()
        
        # 시각화
        plt.figure(figsize=(12, 6))
        plt.plot(daily_counts.index, daily_counts.values, marker='o')
        plt.title('일별 게시물 수 트렌드')
        plt.xlabel('날짜')
        plt.ylabel('게시물 수')
        plt.xticks(rotation=45)
        plt.tight_layout()
        plt.show()
        
        return daily_counts
    
    def analyze_hashtag_trends(self, hashtag_column, top_n=20):
        """
        해시태그 트렌드 분석
        """
        # 모든 해시태그 수집
        all_hashtags = []
        for hashtags in self.data[hashtag_column]:
            if isinstance(hashtags, list):
                all_hashtags.extend(hashtags)
        
        # 빈도 계산
        hashtag_counts = Counter(all_hashtags)
        top_hashtags = hashtag_counts.most_common(top_n)
        
        # 시각화
        hashtags, counts = zip(*top_hashtags)
        
        plt.figure(figsize=(12, 8))
        plt.barh(range(len(hashtags)), counts)
        plt.yticks(range(len(hashtags)), hashtags)
        plt.xlabel('빈도')
        plt.title(f'상위 {top_n} 해시태그')
        plt.tight_layout()
        plt.show()
        
        return top_hashtags
    
    def analyze_user_influence(self, user_column, engagement_column):
        """
        사용자 영향력 분석
        """
        user_influence = self.data.groupby(user_column)[engagement_column].agg([
            'count', 'mean', 'sum'
        ]).reset_index()
        
        user_influence.columns = [user_column, 'post_count', 'avg_engagement', 'total_engagement']
        user_influence = user_influence.sort_values('total_engagement', ascending=False)
        
        return user_influence.head(20)
    
    def create_interaction_network(self, mention_column):
        """
        사용자 상호작용 네트워크 생성
        """
        G = nx.Graph()
        
        # 멘션 관계를 엣지로 추가
        for mentions in self.data[mention_column]:
            if isinstance(mentions, list) and len(mentions) > 1:
                for i in range(len(mentions)):
                    for j in range(i+1, len(mentions)):
                        if G.has_edge(mentions[i], mentions[j]):
                            G[mentions[i]][mentions[j]]['weight'] += 1
                        else:
                            G.add_edge(mentions[i], mentions[j], weight=1)
        
        # 네트워크 시각화
        plt.figure(figsize=(12, 8))
        pos = nx.spring_layout(G, k=1, iterations=50)
        
        # 노드 크기는 degree에 비례
        node_sizes = [G.degree(node) * 50 for node in G.nodes()]
        
        nx.draw(G, pos, 
                node_size=node_sizes,
                with_labels=True,
                node_color='lightblue',
                font_size=8,
                font_weight='bold')
        
        plt.title('사용자 상호작용 네트워크')
        plt.axis('off')
        plt.tight_layout()
        plt.show()
        
        return G

# 사용 예시
analyzer = SocialMediaAnalyzer(processed_tweets)

# 시간별 트렌드
daily_trends = analyzer.analyze_temporal_trends('created_at', 'tweet_id')

# 해시태그 트렌드
top_hashtags = analyzer.analyze_hashtag_trends('hashtags')

# 사용자 영향력
user_influence = analyzer.analyze_user_influence('username', 'like_count')
```

### 저장 및 관리

#### 데이터베이스 저장
```python
import sqlite3
import json
from datetime import datetime

class SocialMediaDataManager:
    def __init__(self, db_path):
        self.db_path = db_path
        self.init_database()
    
    def init_database(self):
        """
        데이터베이스 초기화
        """
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        # 트윗 테이블
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS tweets (
                tweet_id TEXT PRIMARY KEY,
                text TEXT,
                created_at TIMESTAMP,
                author_id TEXT,
                username TEXT,
                retweet_count INTEGER,
                like_count INTEGER,
                reply_count INTEGER,
                hashtags TEXT,
                mentions TEXT,
                sentiment_score REAL,
                collected_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
            )
        ''')
        
        # 기타 소셜미디어 데이터 테이블들...
        
        conn.commit()
        conn.close()
    
    def save_tweets(self, tweets_df):
        """
        트윗 데이터 저장
        """
        conn = sqlite3.connect(self.db_path)
        
        for _, tweet in tweets_df.iterrows():
            cursor = conn.cursor()
            cursor.execute('''
                INSERT OR REPLACE INTO tweets 
                (tweet_id, text, created_at, author_id, username, 
                 retweet_count, like_count, reply_count, hashtags, mentions, sentiment_score)
                VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
            ''', (
                tweet['tweet_id'],
                tweet['text'],
                tweet['created_at'],
                tweet['author_id'],
                tweet['username'],
                tweet['retweet_count'],
                tweet['like_count'],
                tweet['reply_count'],
                json.dumps(tweet.get('hashtags', [])),
                json.dumps(tweet.get('mentions', [])),
                tweet.get('sentiment_score', 0.0)
            ))
        
        conn.commit()
        conn.close()
    
    def get_tweets_by_period(self, start_date, end_date):
        """
        기간별 트윗 조회
        """
        conn = sqlite3.connect(self.db_path)
        
        query = '''
            SELECT * FROM tweets
            WHERE created_at >= ? AND created_at <= ?
            ORDER BY created_at DESC
        '''
        
        df = pd.read_sql_query(query, conn, params=(start_date, end_date))
        conn.close()
        
        return df
```

소셜미디어 데이터 수집은 API 제한, 데이터 품질, 프라이버시 등 다양한 도전과제를 포함한다. 체계적인 접근과 적절한 도구 활용을 통해 소셜미디어에서 의미 있는 인사이트를 도출할 수 있는 고품질 데이터를 수집할 수 있다. 