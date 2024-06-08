```python
import pandas as pd
import Levenshtein
import requests
from io import StringIO

# 레벤슈타인 거리를 이용
class LevenshteinChatBot:
    # 챗봇 객체를 초기화하는 메서드, 초기화 시에는 입력된 데이터 파일을 로드함
    def __init__(self, url):
        self.questions, self.answers = self.load_data(url)

    # URL로부터 질문과 답변 데이터를 불러오는 메서드
    def load_data(self, url):
        response = requests.get(url)
        response.raise_for_status()
        csv_data = StringIO(response.text)
        data = pd.read_csv(csv_data)
        questions = data['Q'].tolist()
        answers = data['A'].tolist()
        return questions, answers

    # 입력 문장에 가장 잘 맞는 답변을 찾는 메서드, 레벤슈타인 거리를 사용하여 가장 높은 유사도를 가 질문의 답변을 반환함
    def find_best_answer(self, input_sentence):
        # 모든 질문에 대해 레벤슈타인 거리를 계산
        distances = [Levenshtein.distance(input_sentence, question) for question in self.questions]
        # 가장 거리가 작은 질문의 인덱스를 찾음
        best_match_index = distances.index(min(distances))
        # 가장 유사한 질문에 해당하는 답변을 반환
        return self.answers[best_match_index]

# 데이터 파일의 경로를 지정합니다.
url = 'https://raw.githubusercontent.com/CUKykkim/chatbot/master/ChatbotData.csv'

# 챗봇 객체를 생성합니다.
chatbot = LevenshteinChatBot(url)

# '종료'라는 입력이 나올 때까지 사용자의 입력에 따라 챗봇의 응답을 출력하는 무한 루프를 실행합니다.
while True:
    input_sentence = input('You: ')
    if input_sentence.lower() == '종료':
        break
    response = chatbot.find_best_answer(input_sentence)
    print('Chatbot:', response)

```
