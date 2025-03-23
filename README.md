# in -GameDev- 3
# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Поляков Александр Дмитриевич
- РИ-230913

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | ? |
| Задание 2 | * | ? |
| Задание 3 | * | ? |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)


## Цель работы
Разработать оптимальный баланс изменения сложности для десяти уровней игры Dragon Picker.

## Задание 1. Начало работы с прототипом
### Ознакомьтесь с презентацией третьей лекции, оцените структуры игры Dragon Picker. Скачайте и ознакомьтесь с прототипом игры Dragon Picker на движке Unity. Запустите игровую сцену, проанализируйте движение дракона:
### - Какие переменные влияют на движение дракона на сцене? Укажите эти переменные.
### - Какие переменные влияют на сложность игры на сцене? Укажите эти переменные.
Ход работы:
-В процессе движения дракона на сцене учитываются два фактора: скорость дракона и случайное значение, которое определяет, будет ли дракон менять направление в определённый момент времени.
-Сложность игры на сцене зависит от нескольких параметров: скорости дракона, частоты появления яйца, скорости падения яйца, количества щитов, частоты изменения направления движения дракона и размера щита.

## Задание 2. Начало работы с шаблоном google-таблицы для балансировки игры
### Ознакомьтесь с презентацией третьей лекции, оцените структуры шаблона таблицы для балансировки. Отметьте, как может быть использован шаблон таблицы для визуализации изменения уровней сложности в игре Dragon Picker?
Ход работы:
- Шаблон таблицы может быть использован для отслеживания изменений переменных «баланса» на разных уровнях. Для наглядности данные можно представить в виде графиков.

## Задание 3. Задания к работе
## - Задание 1. Предложите вариант изменения найденных переменных для 10 уровней в игре. Визуализируйте изменение уровня сложности в таблице.
Ход работы:
- В процессе работы я определил такие параметры: скорость дракона, периодичность появления яйца, число щитов.
- Автоматическое заполнение таблицы выполняется следющим кодом:
```Python
import gspread
import numpy as np
gc = gspread.service_account(filename='unitydatascience-454511-04e86acaa808.json')
sh = gc.open("drag_pick")
sheet = sh.sheet1

def update_headers():
    headers = [
        "Уровень", 
        "Скорость дракона (ед/с)", 
        "Время между яйцами (сек)", 
        "Количество щитов"
    ]
    for idx, header in enumerate(headers):
        col_letter = chr(ord('A') + idx)
        sheet.update_acell(col_letter + "1", header)

def update_indexs():
    for i in range(1, 11):
        sheet.update_acell("A" + str(i + 1), str(i))

def calculate_shields(level: int) -> int:
    if level <= 3:
        return 5
    elif level <= 6:
        return 4
    elif level <= 8:
        return 3
    elif level == 9:
        return 2
    else:
        return 1

def update_game_parameters():
    base_speed = 1.0
    base_time = 5.0

    for level in range(1, 11):
        row = level + 1

        speed = round(base_speed * (1.1 ** level), 2)
        speed_str = str(speed).replace('.', ',')

        drop_time = max(1.0, round(base_time * (0.9 ** level), 2))
        drop_time_str = str(drop_time).replace('.', ',')

        shields = calculate_shields(level)

        sheet.update_acell("B" + str(row), speed_str)
        sheet.update_acell("C" + str(row), drop_time_str)
        sheet.update_acell("E" + str(row), str(shields))

def plot_game_data():
    levels = list(range(1, 11))
    speeds = []
    times = []
    shields = []

    for i in range(2, 12):
        speed = sheet.acell(f"B{i}").value.replace(',', '.')
        time = sheet.acell(f"C{i}").value.replace(',', '.')
        shield = sheet.acell(f"D{i}").value

        speeds.append(float(speed))
        times.append(float(time))
        shields.append(int(shield))

    plt.figure(figsize=(10, 6))
    plt.plot(levels, speeds, marker='o', label="Скорость дракона (ед/с)")
    plt.plot(levels, times, marker='s', label="Время между яйцами (сек)")
    plt.plot(levels, shields, marker='^', label="Количество щитов")

    plt.title("Изменение параметров сложности по уровням")
    plt.xlabel("Уровень")
    plt.ylabel("Значение")
    plt.xticks(levels)
    plt.grid(True)
    plt.legend()
    plt.tight_layout()
    plt.show()


if __name__ == "__main__":
    update_headers()
    update_indexs()
    update_game_parameters()
    plot_game_data()
```
- Таблица описывающая изменения переменных
![image](https://github.com/user-attachments/assets/c386130e-9d32-45fe-b1d1-16a85f53b34a)
Ссылка на таблицу: https://docs.google.com/spreadsheets/d/1HUsx9fzS2M9skJoMe7ZKUz6IdCQrU7kIMfhz5zumE4M/edit?gid=0#gid=0
- Из таблицы видно что скорость дракона и частота появления яйца изменяются равномерно экспоненциально. Я выбрал именно экспоненциальную зависимость, так как она лучше всего равномерно доюавялет сложность игре с каждым уровнем. 
- Количество щитов убывает ступенчато, не по формуле, а по заранее заданным диапазонам.

## - Задание 2. Создайте 10 сцен на Unity с изменяющимся уровнем сложности.
- Для того чтобы передать параметры в заранее сделанные игровые сцены (я просто скопировал 1 сцену Dragon Picker 10 раз ![image](https://github.com/user-attachments/assets/e7618053-4e90-46c0-91a2-88f6b55f9cad)
) я сделал следующие шаги:
1. Создать пустой объект на сцене и прикрепить к нему компонент отвечающий за загрузку данных из таблицы, хранение информации об уровне, выбор и хранение параметров "баланса". Выборка параметров баланса зависит от указанном в сериализованном поле значении уровня.
Так же этот скрипт, после того как считал и определил нужные нам параметры, вызывает событие которое означает что данныве нужно поменять. Это делается для того, чтобюы выдержать паузу перед тем как прочитаются данные из гугл таблицы.

Скрипт компонента:
```C#  
using System;
using System.Collections;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;
using System.Globalization;

public class LoadSettings : MonoBehaviour
{
    [SerializeField] private int _sceneNumber;
    #region Private Fields
    private float _dragonSpeed;
    private float _timeEggDrop;
    private float _mass;
    private int _countShield;
    #endregion

    #region Getters
    public float DragonSpeed { get { return _dragonSpeed; } }
    public float TimeEggDrop { get { return _timeEggDrop; } }
    public float Mass { get { return _mass; } }
    public int CountShield { get { return _countShield; } }
    #endregion

    public event Action CoroutineIsStopped;

    void Awake()
    {
        StartCoroutine(GoogleSheets());
    }
    private float ConvertStrToFloat(string str)
    {
        //Debug.Log(str);
        str = str.Replace(',', '.');
        float result = float.Parse(str, CultureInfo.InvariantCulture.NumberFormat); ;
        return result;
    }
    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1HUsx9fzS2M9skJoMe7ZKUz6IdCQrU7kIMfhz5zumE4M/values/Лист1?key=AIzaSyAY025NahCQGwDFeFa7uJYgIBuRut70nVs");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString())[0];
            //Debug.Log(parseJson);
            string lvl = parseJson[0];
            //Debug.Log(lvl);
            var tryStr = int.TryParse(lvl, out var number);
            if (tryStr == true)
            {
                int intLvl = int.Parse(lvl);
                if (_sceneNumber == intLvl)
                {
                    Debug.Log(intLvl);
                    _dragonSpeed = ConvertStrToFloat(parseJson[1]);
                    _timeEggDrop = ConvertStrToFloat(parseJson[2]);
                    string strCount = parseJson[3];
                    _countShield = Convert.ToInt32(strCount);
                    //Debug.Log($"Скорость дракона: {_dragonSpeed}, " +
                    //    $"Скорость появления яйца: {_timeEggDrop}, " +
                    //    $"Скорость падения яйца: {_mass}, " +
                    //    $"Количество щитов: {_countShield}");
                }
            }
        }
        CoroutineIsStopped?.Invoke();

    }
}
```
Объект отвечающий за загрузку параметров LoadSettings, их хранение и их передачу:
![image](https://github.com/user-attachments/assets/f67a48a2-6ef3-4327-a30c-f229506ba02a)


2. Далее нужно залезть в код в котором определяется и реализуется поведение дракона, а так же другой скрипт который делает тоже самое но с щитом
3. Нужные нам скрипты в файловой системе юнити:
![image](https://github.com/user-attachments/assets/71e8fdcc-0c71-41cf-a1d5-93878aeefad6)


В скрипте отвечающим за дракона мы создаем обработчик события, который будет обновлять нужные нам параметры:
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class DragonPicker : MonoBehaviour
{
    [SerializeField] private LoadSettings _settings;
    public GameObject energyShieldPrefab;
    public int numEnergyShield;
    public float energyShieldBottomY = -6f;
    public float energyShieldRadius = 1.5f;
    
    public List<GameObject> shieldList;

    private void OnEnable()
    {
        _settings.CoroutineIsStopped += UpdateShield;
    }

    void CreateShield()
    {
        shieldList = new List<GameObject>();

        for (int i = 1; i <= numEnergyShield; i++)
        {
            GameObject tShieldGo = Instantiate<GameObject>(energyShieldPrefab);
            tShieldGo.transform.position = new Vector3(0, energyShieldBottomY, 0);
            tShieldGo.transform.localScale = new Vector3(1 * i, 1 * i, 1 * i);
            shieldList.Add(tShieldGo);
        }
    }

    private void UpdateShield()
    {
        numEnergyShield = _settings.CountShield;
        CreateShield();
    }

    public void DragonEggDestroyed(){
        GameObject[] tDragonEggArray = GameObject.FindGameObjectsWithTag("Dragon Egg");
        foreach (GameObject tGO in tDragonEggArray){
            Destroy(tGO);
        }
        int shieldIndex = shieldList.Count - 1;
        GameObject tShieldGo = shieldList[shieldIndex];
        shieldList.RemoveAt(shieldIndex);
        Destroy(tShieldGo);

        if (shieldList.Count == 0){
            SceneManager.LoadScene("_0Scene");
        }
    }
}
```

И делаем тоже самое в скрипте который создает щит:
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyDragon : MonoBehaviour
{
    public GameObject dragonEggPrefab;
    [SerializeField] private LoadSettings _settings;
    public float speed;
    public float timeBetweenEggDrops;
    public float leftRightDistance = 10f;
    public float chanceDirection = 0.1f;

    private void OnEnable()
    {
        _settings.CoroutineIsStopped += UpdateFields;
    }


    void Start()
    {
        Invoke("DropEgg", 2f);
    }

    private void UpdateFields()
    {
        speed = _settings.DragonSpeed;
        //Debug.Log(speed);
        timeBetweenEggDrops = _settings.TimeEggDrop;
        //Debug.Log(timeBetweenEggDrops);
    }

    void DropEgg(){
        Vector3 myVector = new Vector3(0.0f, 5.0f, 0.0f);
        GameObject egg = Instantiate<GameObject>(dragonEggPrefab);
        egg.transform.position = transform.position + myVector;
        Invoke("DropEgg", timeBetweenEggDrops);
    }

    // Update is called once per frame
    void Update()
    {
        Vector3 pos = transform.position;
        pos.x += speed * Time.deltaTime;
        transform.position = pos;

        if (pos.x < -leftRightDistance){
            speed = Mathf.Abs(speed);
        }
        else if (pos.x > leftRightDistance){
            speed = -Mathf.Abs(speed);
        }
    }

    private void FixedUpdate() {
        if (Random.value < chanceDirection){
            speed *= -1;
        }
    }
}
```

Далее нужно передать каждому объекту, который реализует функционал дракона и щита, ссылку на скрипт который определяет параметры: 
![image](https://github.com/user-attachments/assets/6b6c2363-7b30-46ba-ab78-adfa504b2fae)


И так нужно сделать с каждой сценой

Итог (для примера был взят последний 10 уровень, в котором отображены измененный параметры в инспекторе):
![image](https://github.com/user-attachments/assets/4f02343c-24e7-4222-bd99-e9fb2b16b715)




Для визуализации изменений параметров был обновлен код заполнения таблицы:

![image](https://github.com/user-attachments/assets/02caa6df-fc07-47d1-b192-c4a130b828ef)


## Выводы
Я научился работать с параметрами баланса, визуализировать их, расчитывать, а также применять алгоритм изменения параметров в юнити, с помощью Python и гугл таблиц

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
