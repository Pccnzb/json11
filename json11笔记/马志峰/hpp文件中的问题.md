��`json11::Json`����ڲ���ʹ��JsonValue������ʾJson��������Ҫ�����͡�����䷭��Ĳ��ã���ӭ�ṩ���õķ��룩


 �й�numbers������Ҫ�ر�˵��һ��
 
JSON���﷨�����������͸������������������ϲ������֡����ԣ��е�JSONʵ�����������͸���������Щ�����֡�

��json11�У����ǲ����������͸���������Ϊ��һЩJSON��ʵ���У�����Javascript���������ֶ�������ͬһ�����ͣ�����JSONȥ�����������ֱ�`round-trip`��ʽת����

���Ƿǳ�Σ�յ�! Ϊ�˹��������գ�json11�����ֶ�����Ϊdouble���ͣ�ͬʱ�ṩһЩ�����ĸ���������

double�ľ���IEEE754 ('double')���Ծ�ȷ�ı��� `+/-2^53`��Χ�ڵ�����,���˵��ǣ������ϵͳ��'int'���������Χ�ڡ�(ʱ���ͨ��ʹ�� int64����long long������Y2038K���⣻a double storing microseconds since some epoch will be exact for `+/- 275` years.

��������ɶ��˼û������

```c++
#pragma once
```

������൱��ͷ�ļ���������ȫ�ȼ���

```c++
#ifdef _MSC_VER  
    #if _MSC_VER <= 1800 // VS 2013  
        #ifndef noexcept  
            #define noexcept throw()  
        #endif  

        #ifndef snprintf  
            #define snprintf _snprintf_s  
        #endif  
    #endif  
#endif  
```

�����define��ʲô��;��

```C++
class Json final {  
```

final��ʲô��;��

```c++
Json() noexcept;  
```

���������noexceptʲô�﷨��
    
```c++
Json(std::nullptr_t) noexcept;  
```

���`std::nullptr_t`��ʲô������  
Ϊʲôʹ��������ͣ�
    
```c++
Json(std::string &&value);  
Json(array &&values);  
Json(object &&values);  
```

`&&`��ʲô��˼��
    

```c++
template <class T, class = decltype(&T::to_json)>  
Json(const T & t) : Json(t.to_json()) {}  
```

ģ����Ķ��壬Ҫ��һ����

```c++
// Implicit constructor: map-like objects (std::map, std::unordered_map, etc)  
template <class M, typename std::enable_if<
    std::is_constructible<std::string, typename M::key_type>::value
    && std::is_constructible<Json, typename M::mapped_type>::value,
        int>::type = 0>  
Json(const M & m) : Json(object(m.begin(), m.end())) {}           
```

ע��˵��ֻҪ����һ��`map-like`�Ķ���Ϳ��ԣ�������ô�����ģ�

    
```c++
// Implicit constructor: vector-like objects (std::list, std::vector, std::set, etc)  
template <class V, typename std::enable_if<
    std::is_constructible<Json, typename V::value_type>::value,
        int>::type = 0>  
Json(const V & v) : Json(array(v.begin(), v.end())) {}  
```

ͬ�ϣ�`vector-like`

```c++
// This prevents Json(some_pointer) from accidentally producing a bool. Use  
// Json(bool(some_pointer)) if that behavior is desired.  
Json(void *) = delete;  
```
    
ɶ��˼��

```c++
void dump(std::string &out) const;  
std::string dump() const {  
    std::string out;  
    dump(out);  
    return out;  
}  
```

�ṩ�����ֲ�ͬ�Ľ��������ʽ

```c++
const char * in;  
std::string(in);  
```

����ǵ��ˣ�string���������죿


```c++
static inline std::vector<Json> parse_multi(  
    const std::string & in,  
    std::string & err,  
    JsonParse strategy = JsonParse::STANDARD) {  
    std::string::size_type parser_stop_pos;  
    return parse_multi(in, parser_stop_pos, err, strategy);  
}  
```

`std::string::size_type parser_stop_pos;`Ĭ�ϳ�ʼ���õ���ֵ�Ƕ��٣�

```c++
bool operator== (const Json &rhs) const;  
bool operator<  (const Json &rhs) const;  
bool operator!= (const Json &rhs) const { return !(*this == rhs); }  
bool operator<= (const Json &rhs) const { return !(rhs < *this); }  
bool operator>  (const Json &rhs) const { return  (rhs < *this); }  
bool operator>= (const Json &rhs) const { return !(*this < rhs); }  
```

�Ƚ������ֻ��Ҫʵ��`==`��`<`�Ϳ����ˣ�������ͨ���������õ���

```c++
private:  
    std::shared_ptr<JsonValue> m_ptr;  
```

��֪�������ݶ���װ��JsonValue���еĺô���

```c++
// Internal class hierarchy - JsonValue objects are not exposed to users of this API.  
```

���������е�һ���ô���û�б�¶���û���

```c++

class JsonValue {  
protected:  
    friend class Json;  
    friend class JsonInt;  
    friend class JsonDouble;  
    virtual Json::Type type() const = 0;  
    virtual bool equals(const JsonValue * other) const = 0;  
    virtual bool less(const JsonValue * other) const = 0;  
    virtual void dump(std::string &out) const = 0;  
    virtual double number_value() const;  
    virtual int int_value() const;  
    virtual bool bool_value() const;  
    virtual const std::string &string_value() const;  
    virtual const Json::array &array_items() const;  
    virtual const Json &operator[](size_t i) const;  
    virtual const Json::object &object_items() const;  
    virtual const Json &operator[](const std::string &key) const;  
    virtual ~JsonValue() {}  
};  
```

���������࣬��Ҫ�࿴
