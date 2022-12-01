# LR-3

Кириченко Антон 

ЭВТ-70

Игровой Движок: Unity

Лабораторная работа 3
Тема: разработка QUIZ игры

Цель: разработать игру QUIZ

Теоретическая часть:

Задание: Разработать игру по видеоматериалу и создать отчёт.

Ход работы:

1.	Создание текстового вопроса:
с помощью Инспектора и Hierarchy (объекта(-ов) в ней),
мы создаём объект в Hierarchy: QuizManager и настраиваем его с помощью Инспектора
 ![image](https://user-images.githubusercontent.com/119482774/205071705-31b680a9-071b-44b7-8b2c-dc23165ec311.png)

Рисунок 3.1 Настройка Вопроса(-ов)
![image](https://user-images.githubusercontent.com/119482774/205071728-e037e15a-bead-44f5-9994-2934d5ae2574.png)

 
Рисунок 3.2 Создание объектов в Hierarchy: QuizManager , Canvas
2.	Создание звукового вопроса, происходит в инспекторе
![image](https://user-images.githubusercontent.com/119482774/205071783-f0b2aa49-aa5a-4a34-bdbe-8f8d548febc0.png)

 
Рисунок 3.3 Добавление Звука \ Музыки / звукового Эффекта для вопроса №2

3.	Создание анимационного вопроса 
В инспекторе мы Добавили Анимацию к вопросу
 ![image](https://user-images.githubusercontent.com/119482774/205071814-5297a7be-a675-4816-842d-44340305770f.png)

Рисунок 3.4 Добавления Анимаций к вопросу
4.	Создание бланков вопросов (добавление самих вопросов)
 ![image](https://user-images.githubusercontent.com/119482774/205071849-d19406c3-f79b-4e65-9104-b9db0a8c57f0.png)

Рисунок 3.5 Бланки вопросов с картинками созданы с помощью объекта(-ов) и настроены в Инспекторе
```
5.	Код для создания Вопросов: 
Код 1)
public class QuizManager : MonoBehaviour
{
    [SerializeField] private QuizUI quizUI;
    [SerializeField]
    private List<Question> questions;
    private Question selectedQuestion;
    void Start()
    {
        SelectQuestion();
    }
    void SelectQuestion()
    {
        int val = Random.Range(0, questions.Count);
        selectedQuestion = questions[val];
        quizUI.SetQuestion(selectedQuestion);
    }
    public bool Answer(string answered)
    {
        bool correctAns = false;
        if(answered == selectedQuestion.correctAnswer)
        {
            correctAns = true;
        }
        else
        {
        }
        Invoke("SelectQuestion", 0.4f);
        return correctAns;
    }
}
[System.Serializable]
public class Question
{
    public string questionInfo;
    public QuestionType questionType;
    public Sprite questionImg;
    public AudioClip qustionClip;
    public UnityEngine.Video.VideoClip questionVideo;
    public List<string> options;
    public string correctAnswer;
}
[System.Serializable]
public enum QuestionType
{
    TEXT,
    IMAGE,
    VIDEO,
    AUDIO
}
 Код 2)
public class QuizUI : MonoBehaviour
{
    [SerializeField] private QuizManager quizManager;
    [SerializeField] private TextMeshProUGUI questionText;
    [SerializeField] private Image questionImage;
    [SerializeField] private UnityEngine.Video.VideoPlayer questionVideo;
    [SerializeField] private AudioSource questionAudio;
    [SerializeField] private List<Button> options;
    [SerializeField] private Color correctCol, wrongCol, normalCol;
    private Question question;
    private bool answered;
    private float audioLength;
    void Awake()
    {
        for(int i = 0; i < options.Count; i++)
        {
            Button button = options[i];
            button.onClick.AddListener(() => OnClick(button));
        }
    }
    public void SetQuestion(Question question)
    {
        this.question = question;
        switch (question.questionType)
        {
            case QuestionType.TEXT:
                questionImage.transform.parent.gameObject.SetActive(false);
                break;
            case QuestionType.IMAGE:
                ImageHolder();
                questionImage.transform.gameObject.SetActive(true);
                questionImage.sprite = question.questionImg;
                break;
            case QuestionType.VIDEO:
                ImageHolder();
                questionVideo.transform.gameObject.SetActive(true);
                questionVideo.clip = question.questionVideo;
                questionVideo.Play();
                break;
            case QuestionType.AUDIO:
                ImageHolder();
                questionAudio.transform.gameObject.SetActive(true);
                audioLength = question.qustionClip.length;
                StartCoroutine(PlayAudio());
                break;
        }
        questionText.text = question.questionInfo;
        List<string> answerList = ShuffleLists.ShuffleListItems<string>(question.options);
        for(int i = 0; i < options.Count; i++)
        {
            options[i].GetComponentInChildren<TextMeshProUGUI>().text = answerList[i];
            options[i].name = answerList[i];
            options[i].image.color = normalCol;
        }
        answered = false;
    }

    IEnumerator PlayAudio()
    {
        if(question.questionType == QuestionType.AUDIO)
        {
            questionAudio.PlayOneShot(question.qustionClip);
            yield return new WaitForSeconds(audioLength + 0.5f);
            StartCoroutine(PlayAudio());
        }
        else
        {
            StopCoroutine(PlayAudio());
            yield return null;
        }
    }
    void ImageHolder()
    {
        questionImage.transform.parent.gameObject.SetActive(true);
        questionImage.transform.gameObject.SetActive(false);
        questionAudio.transform.gameObject.SetActive(false);
        questionVideo.transform.gameObject.SetActive(false);
    }
    private void OnClick(Button button)
    {
        if (!answered)
        {
            answered = true;
            bool val = quizManager.Answer(button.name);
            if (val)
            {
                button.image.color = correctCol;
            }
            else
            {
                button.image.color = wrongCol;
            }
        }
    }
}
Код 3) 
static public class ShuffleLists
{
    public static List<T> ShuffleListItems<T>(this List<T> list)
    {
        int n = list.Count;

        while (n > 1)
        {
            n--;
            int k = Random.Range(0, n);
            T value = list[k];
            list[k] = list[n];
            list[n] = value;

        }
        return list;
    }
}
```
Вывод: В ходе проделанной работы стало понятно, как разрабатывать QUIZ игры
[ЛР3. Создание QUIZ игры.zip](https://github.com/Userfall3000/LR-3/files/10132989/3.QUIZ.zip)

