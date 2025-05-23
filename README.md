# Тест разработчика C# №2 (шаблон)

<aside>
⚠️ Отвечайте самостоятельно, не стремитесь подсмотреть в справочниках, гугле и тому подобных. Если не знаете ответ на вопрос - совершенно нормально его пропустить. Качество и самостоятельность ответов будем обсуждать на очных интервью, поэтому предлагаем не читерить и экономить Ваше и наше время.
  
⚠️ Публикуйте свой Gist с расширением MD чтобы сохранить форматирование и читаемость.

</aside>

# 1. Общие вопросы

### 1.1 String и DateTime

Что будет выведено в консоль и почему?

```csharp
class Program {
  static String location;
  static DateTime time;
 
  static void Main() {
    Console.WriteLine(location == null ? "location is null" : location);
    Console.WriteLine(time == null ? "time is null" : time.ToString());
  }
}
```

---

> Ваш ответ

location is null (ссылочный тип, может быть null)

что-то вроде 0000-00-01 00:00.0000 (значимый тип, не может быть null)

### 1.2 Enumerators

Что будет выведено в консоль?

```csharp
IEnumerable<int> EnumerateInts(int length) {
  for (var i = 0; i < length; i++) {
    Console.WriteLine(i);
    yield return i;
  }
}

var items = EnumerateInts(3);
```

---

> Ваш ответ

3, потому что обращение только одно

### 1.3 Базовые классы List

Перечислите приблизительно на память от каких базовых классов наследуется `List<>` и какие интерфейсы он реализует?

---

> Ваш ответ

Базовый класс - Object, интерфейсы помню IEnumerable<T>, IList<T>, ICollection<T>

# 2. Entity Framework

### 2.1 Запросы

Какие запросы (приблизительно) будут направлены в SQL-сервер во время исполнения следующего кода:

```csharp
class Student {
  public int Id {get;set;}
  public string Name {get;set;}
  public int Course {get;set;}
  public DateTime BirthDate {get;set;}
}

var student = context.Students.FirstOrDefault(s => s.Id == 1);
student.Name = "Oleg";
context.SaveChanges();
```

---

> Ваш ответ

Тонкостей не знаю, но чисто предположительно:

SELECT * FROM Students WHERE Id == 1

UPDATE Students SET Name = "Oleg" WHERE Id == 1

### 2.2 Трекинг изменений

Для чего предназначен метод `IQueryable.AsNoTracking()`? Когда его следует использовать?

---

> Ваш ответ

Не отслеживает изменения полученных моделей, т.е. при context.SaveChanges изменения в БД внесены не будут. Можно использовать если мы получаем данные только для чтения

### 2.3 Запросы

Что произойдёт при выполнении первой строки и второй? Какого типа будут переменные `items1` и `items2`?

```csharp
var items1 = context.Students.Take(5).ToList();
var items2 = context.Students.ToList().Take(5);
```

---

> Ваш ответ

items1 - получаем IQueryable, делаем SQL-запрос на 5 первых предметов, преобразовываем в List<Student>, на выходе List

items2 - получаем IQueryable, сразу преобразовываем к List и делаем LINQ-запрос на 5 первых элементов, на выходе List

# 3. Dependency Injection

### 3.1. Жизненный цикл

Опишите чем отличается регистрация типов или объектов методами `.AddTransient()`, `.AddScoped()` и `.AddSingleton()`?

---

> Ваш ответ

AddTransient - создается каждый раз, как затребован

AddScoped - создается под каждый HTTP запрос, в рамках HTTP запроса может быть использован несколько раз 

AddSingleton - создается один раз за все время жизни программы, все обращения идут через этот экземпляр

# 4. Разное

### 4.1. Паттерн MVVM

Что такое MVVM? Почему он обрёл популярность именно в WPF?

> Ваш ответ

Model-View-ViewModel, разделение на бизнес-логику (модель) <-> логику интерфейса (модель представления) <-> интерфейс(представление)
	потому что WPF изначально предполагает собой именно такую модель, т.к. для интерфейса создается .xml-файл с описанием элементов интерфейса, к которому прикреплен .cs-файл, имеющий доступ к этим элементам и который может обращаться к не относящимся к UI классам 

### 4.2. INotifyPropertyChanged

Опишите состав интерфейса INotifyPropertyChanged. В чём разница?

```csharp
PropertyChanged("City");
PropertyChanged(null);
```

> Ваш ответ

Честно подсмотрел в свой WPF-проект: предполагает реализацию в классе окна, требует event с типом PropertyChangedEventHandler и реализацию функции OnPropertyChanged, PropertyChanged("City") сообщит UI-элементам, привязанным к элементу City, что свойство изменилось, а поведение PropertyChanged(null) не знаю, скорее всего ошибку выдаст

### 4.3 Методы HTTP-протокола

Объясните разницу между PUT, POST, PATCH.

> Ваш ответ

не имеют "захардкоженных" функций, но общий смысл понимаю так:

PUT - создание элемента

POST - общий запрос на какое-либо изменение - либо создание, либо изменение

PATCH - некое изменение

# 5. Задачи

### 5.1 Проектирование БД

Спроектируйте таблицы в реляционной БД для хранения двумерных матриц произвольного размера (M**×**N). Укажите в свободной форме состав полей таблицы/таблиц.

---

> Ваш ответ

3 таблицы:

Matrices, 3 столбца: ширина, высота, ключ на строку из таблицы Columns

Columns, 2 столбца: ключ на строку из таблицы Matrices, строка с перечислением ключей из таблицы Rows

Rows, 2 столбца: ключ на строку из таблицы Rows, строка с перечислением значений в конкретной строке матрицы 

Логику записи/чтения придется делать на бэкенде, не настолько сведущ в SQL чтобы сделать лучше

### 5.2 Уникальный символ

Напишите реализацию функции, которая принимает на вход строку из символов `a-z`, а на выходе выдаёт первый уникальный символ (который встречается в строке ровно 1 раз), либо `null`, если такого символа нет.

---

```jsx
public static char? FirstUnique(string str) {
    bool nonUnique = false;
	char? c = null;
	List<char> rejects = new List<char>();
	for (var i = 0; i < str.Length; i++)
	{
		c = str[i];
		if (rejects.Contains(str[i])) {
			c = null;
			continue;
		}
		for (var j = i+1; j < str.Length; j++)
		{
			if (c == str[j])
			{
				nonUnique = true;
				break;
			}
		}
		if (nonUnique)
		{
			rejects.Add(str[i]);
		}
		else
		{
			return c;
		}
		nonUnique = false;
	}
	return c;
}
```
