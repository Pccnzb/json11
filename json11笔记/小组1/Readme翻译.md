json11
------

json11��һ����������C++11��, �ṩJSON�����л��ͷ����л�����.

���ĵĶ����� `json11::Json`. ����������ʾ�������͵�JSON���ݣ�`null`, `bool`, `number (int or double)`, `string (std::string)`, `array (std::vector)`, ����`object (std::map)`.

`json11::Json`���͵Ķ��������ֵ����һ����֧�ָ�ֵ�����������ݡ��ȽϵȲ���. ���ǻ��ṩ�˸�������
- `Json::dump`������`json11::Json`���͵Ķ������л�Ϊstring
- `Json::parse (static)`������std::string�����л�Ϊ`json11::Json`���͵Ķ���

ʹ��C++11�ṩ�ĳ�ʼ�������Ժ����״���һ��`json11::Json`����:

```c++
Json my_json = Json::object {
    { "key1", "value1" },
    { "key2", false },
    { "key3", Json::array { 1, 2, 3 } },
};
std::string json_str = my_json.dump();
```

���ﻹ�ṩ��һЩ���õĹ��캯�������Խ���׼�����ͺ��û��Զ��������Զ���ת��Ϊ`json11::Json`��������:

```c++
class Point {
public:
    int x;
    int y;
    Point (int x, int y) : x(x), y(y) {}
    Json to_json() const { return Json::array { x, y }; }
};

std::vector<Point> points = { { 1, 2 }, { 10, 20 }, { 100, 200 } };
std::string points_json = Json(points).dump();
```

`json11::Json`ͬʱ֧���±�͹ؼ���������

```c++
Json json = Json::array { Json::object { { "k", "v" } } };
std::string str = json[0]["k"].string_value();
```

δ�������

����������鿴`json11.hpp`��
