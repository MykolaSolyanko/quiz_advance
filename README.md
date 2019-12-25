1. В соответсвии с кодом ниже напишите правильные реализации конструкторов
```cpp
class Parent {
  public:
    Parent(int x, char* str, int y);
};

class Child: public Parent {
  public:
    Child(int x, int y);
};

class GrandChild: public Child {
  public:
    GrandChild(int x);
};

```

2. Ниже выберите вариант, почему параметром копируещего конструктора должна быть ссылка?
```
a. Потому что передавать константную ссылку намного эфективней чем копию;
b. Потому что ссылка может быть изменена вызываемым конструктором;
c. Потому что копия будет вызывать копирующий конструтор рекурсивно;
d. Потому что класс не может содержать указателя на свой тип, он должен использовать ссылку;
```

3. Почему важно явно определять конструктор копирования или оператор присвоения когда класс содержит ресурс, например указатель на динамическую память?
```
a. Потому что дефолтный копирующий конструктор и оператор присвоения только копируют значение указетелей.
b. Потому что значения указателя не сохраняются в дефолтном конструкторе копирования и операторе присвоения.
с. Потому что дефолтный конструктор копироваания удаляет все указатели.
d. Потому что дефолтный конструтор использует глубокое копирование.
e. Потому что указатели должны быть скопированы используя бинарные операторы.
```

4. В соответсвии с кодом ниже напишите праильную реализацию оператора присвоения
```cpp
class Foo {
  public:
    const Foo& operator= (const Foo& rhs);
  private:
    int x;
};

class Bar: public Foo {
  public:
    const Bar& operator= (const Bar& rhs);
  private:
    int y;
};
```

5. Ниже выбирите правильную сигнатуру перемещающего конструктора и перемещающего оператора. И обьясните почему так
```cpp
a) Foo(Foo& rhs); const Foo& operator=(Foo& rhs);
b) Foo(const Foo& rhs); const Foo& operator=(const Foo& rhs);
c) Foo(Foo** rhs); const Foo& operator=(Foo** rhs);
d) Foo(const Foo&& rhs); const Foo& operator=(const Foo&& rhs);
e) Foo(Foo&& rhs); const Foo& operator=(Foo&& rhs);
```

6. Какой из вариантов ниже являеться коректным
```cpp
#include <string>
#include <utility>

class Test{
  public:
    int i;
    bool b;
    std::string s;
};

int main() {
  Test t{42, true, "test"};
  Test t2 = std::move(t);
}
```
```
a) Это не будет компилироваться
b) Будет брошено исключение
с) Все будет работать коректно `t.i = 0, t.b = false, t.s=""`
d) Это будет работать `t.i = 42, t.b = true, t.s="test"`
e) Это будет работать, `t` останеться в валидном unspecified состоянии `t.i = 42, t.b = true, t.s=""`
```

7. Что будет выведенно на экран
```cpp
#include <iostream>
#include <functional>

class Test {
  public:
   Test() = default;
   Test(const Test&) {
     std::cout << "copy" << std::endl;
   }
   Test(Test&&) {
     std::cout << "move" << std::endl;
   }
   void set_callback(const std::function<void()>& cb) {
     std::cout << "set copy callback" << std::endl;
     func = cb;
   }

   void set_callback(std::function<void()>&& cb) {
     std::cout << "set move callback" << std::endl;
     func = cb;
   }

  private:
   std::function<void()> func;
};

void process_test(Test&& test) {
    Test new_test{test};
    new_test.set_callback([](){});
}

int main() {
  Test test;

  auto cb = [](){};
  test.set_callback(cb);

  process_test(std::move(test));

  Test test2;
  Test&& test_move = std::move(test2);
  return 0;
}
```

8. Что являеться правильным для std::move по отношении к классу A?
```
a) Будет ошибка компиляции если в классе А нет дефолтного конструктора.
b) Будет ошибка времени выполнения если класс А не поддерживает семантику перемещения.
c) std::move будет только копировать члены класса А, которые пренадлежат фундаментальным типам.
d) std::move будет только перемещать члены класса А, которые пренадлежат пользовательским типам.
e) std::move это только оператор приведения типа.
```

9. Опишите ситуацию когда и зачем зарещают семантику копирования для класса? Напишите класс (только сигнатуры без реализации) в котором запрещено копирование, а разрешаеться только перемещать.

10. Что нужно сделать чтобы следующий код компилировался?
```cpp
typedef void (*Callback)(int sum);
void CalcSumAsync(int a, int b, Callback c) {
    c(a + b);
}

int main() {
  int a = 100;
  int b = 10;
  CalcSumAsync(a, b, [](int sum) {
    std::cout << a << " + " << b << " = " << sum;
  });
  return 0
}
```

11. Что в этом коде не так? и напишите как исправить
```cpp
#include <iostream>
#include <exception>
class A {
  public:
    A () = default;
    void write_data_to_db(size_t data) {
        if (data == 10) {
            throw std::runtime_error{"data == 10"};
        }
    }
};

void make_array(size_t size, int data) {
    A* array = new A[size];
    for (size_t i = 0; i < size; ++i) {
      array[i].write_data_to_db(data);
    }
    delete [] array;
}

int main() {
  try {
   make_array(30, 10);
  } catch(std::exception& e) {
    std::cout << e.what() << std::endl;
  } catch (std::runtime_error& e) {
    std::cout << e.what() << std::endl;
  }
  return 0;
}
```

12. Будет ли в следующем фрагменте кода происходить `stack unwinding`, если да то что будет выведенно на экран, если нет то что нужно исправить чтобы он происходил
```cpp

#include <iostream>
#include <exception>
class A {
  public:
    A () {
      std::cout << "A" << std::endl;
    }
    ~A () {
      std::cout << "~A" << std::endl;
    }
    void func_throw_exception() {
        throw std::logic_error{"throw exception from class A"};
    }
};

void call_func2() {
  A a;
  a.func_throw_exception();
}

void call_func1() {
    A a;
    call_func2();
}

int main() {
  call_func1();
  return 0;
}
```

13. Что будет выведенно на экране
```cpp

#include <iostream>
#include <exception>
class A {
  public:
    void func_throw_exception() {
        throw std::logic_error{"throw exception from class A"};
    }
};

void call_func2() {
  A a;
  try {
    a.func_throw_exception();
  } catch(const std::exception e) {
      std::cerr << e.what() << '\n';
      throw;
  }
}

void call_func1() {
    A a;
    try {
      call_func2();
    } catch(const std::exception& e) {
        std::cerr << e.what() << '\n';
    }
}

int main() {
  call_func1();
  return 0;
}
```

14. Что будет выведено на экране?
```cpp
#include <iostream>
#include <exception>
class ClassWithException {
  public:
    void throw_exception() {
       throw std::logic_error{"throw exception from class A"};
    }
};
class A {
  public:
    void function() noexcept {
        object.throw_exception();
    }
  private:
   ClassWithException object;
};

void call_func() {
  A a;
  try {
    a.function();
  } catch(const std::exception& e) {
      std::cerr << e.what() << '\n';
  }
}

int main() {
  call_func();
  return 0;
}
```

15. Что произойдет если деструктор класса `A` бросает исключение, и мы его не хендлим в этом же деструкторе, а например добавляем блок `catch` в функции или методе который создает обьект класса `A`?

16. Что выводит на экран следующий код?
```cpp
#include <iostream>
#include <thread>
#include <string>

void print_n_count_string(const std::string& str, size_t count) {
  for (size_t i = 0; i < count; i++) {
    std::cout << str << std::endl;
  }
}
int main() {
    std::thread{print_n_count_string, "Hello World", 10};
}
```

17. Что будет выведено на экран?
```cpp
#include <iostream>
#include <thread>
#include <string>
#include <future>

void set_value(std::promise<int> prom) {
  std::this_thread::sleep_for(std::chrono::seconds(4));
  prom.set_value(10);
}
int main() {
  std::promise<int> prom;
  auto f = prom.get_future();
  std::thread t {set_value, std::move(prom)};
  t.detach();

  if (f.wait_for(std::chrono::seconds(1)) != std::future_status::ready) {
    t.join();
  }
  std::cout << f.get() << std::endl;
}
```

18. Что будет выведенно на экран
```cpp
#include <exception>
#include <future>
#include <iostream>
#include <string>
#include <thread>

static size_t global_value{1};
void set_value(int value) {
  if (value % 2 != 0) {
    throw std::invalid_argument{"Value isn't correct"};
  }
  global_value = value;
}

int main() {
  try {
    std::thread t{set_value, 11};
    t.detach();

    while (global_value != 11) {
      std::this_thread::sleep_for(std::chrono::seconds(1));
    }
    std::cout << global_value << std::endl;
  } catch (std::exception &e) {
    std::cout << e.what() << std::endl;
  }
  return 0;
}
```

19. Что будет выведенно на экран? и обьясните почему так?
```cpp
#include <iostream>
#include <thread>
#include <future>

void set_value() {
  std::cout << "1" << std::endl;
  std::this_thread::sleep_for(std::chrono::seconds(1));
  std::cout << "2"<< std::endl;
}

int main() {
  std::async(std::launch::async, set_value);

  std::cout << "3"<< std::endl;
  return 0;
}
```

20. Опишите все возможные проблемные ситуации кода ниже, и как бы вы их решили
```cpp
#include <iostream>
#include <thread>
#include <future>
#include <mutex>

std::mutex mut;
std::condition_variable cv;
size_t value = 10;
void wait_event() {
  std::unique_lock<std::mutex> l(mut);
  std::this_thread::sleep_for(std::chrono::seconds(1));
  cv.wait(l);
  std::cout << value << std::endl;
}

void notify() {
  value = 30;
  cv.notify_all();
}

int main() {
  auto f_wait = std::async(std::launch::async, wait_event);
  auto f_notify = std::async(std::launch::async, notify);
  f_notify.get();
  f_wait.get();
  return 0;
}
```

21. Сработает ли `assert` в данном коде? Опишите ваш вариант ответа.
```cpp
#include <iostream>
#include <thread>
#include <future>
#include <cassert>

size_t value = 10;
bool wait_set = true;
void notify() {
  value = 40;
  wait_set = false;
}

void wait_event() {
  while(wait_set) {
    ;
  }
  assert(value == 40);
}

int main() {
  auto f_wait = std::async(std::launch::async, wait_event);
  auto f_notify = std::async(std::launch::async, notify);
  f_wait.get();
  f_notify.get();
  return 0;
}
```

22. Обьясните какие могут быть проблемы в этом коде?
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <random>
#include <iterator>

int main() {

  std::vector<int> v(5);
  std::random_device rd;
  std::uniform_int_distribution<int> dist(1, 100);
  std::generate(v.begin(), v.end(), [&dist, &rd](){ return dist(rd); });

  std::copy_if(v.begin(), v.end(), std::back_inserter(v), [](int value) { return value == 85; });

  std::copy(v.begin(), v.end(), std::ostream_iterator<int>(std::cout, " , "));
  std::cout.flush();
  return 0;
}
```

23. Каким существующим алгоритмом с стандартной библиотеке(STL) можно заменить этот велосипед?
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <random>
#include <iterator>

using int_iterator = std::vector<int>::iterator;
using const_int_iterator = std::vector<int>::const_iterator;

bool check_correct_data(int_iterator begin, const_int_iterator end, int check_value) {
  while (begin != end) {
    if (*begin++ != check_value) {
      return false;
    }
  }
  return true;
}

int main() {

  std::vector<int> v(5);
  std::random_device rd;
  std::uniform_int_distribution<int> dist(1, 100);
  std::generate(v.begin(), v.end(), [&dist, &rd](){ return dist(rd); });

  if (check_correct_data(v.begin(), v.end(), 10)) {
    std::cout << "vector elements satisfy a given criterion" << std::endl;
  }

  return 0;
}
```

24. Какие строчки подойдут для вставки в обозначенном месте, чтобы на экран вывело "12345" ? Множественный выбор, т.е. вариантов может быть несколько.
```cpp
#include <iostream> 
#include <algorithm> 
#include <iterator> 
#include <vector> 
 
int main() 
{ 
    std::vector<int> v = {1, 4, 2, 5, 3, 2, 3}; 
    // paste here
}
```
 a)
   ```cpp
   std::sort(v.begin(), v.end()); 
   std::vector<int>::iterator it = std::unique(v.begin(), v.end()); 
   std::copy(v.begin(), it, std::ostream_iterator<int>(std::cout));
   ```

 b)
   ```cpp
    std::vector<int>::iterator it = std::unique(v.begin(), v.end()); 
    std::sort(v.begin(), it); 
    std::unique_copy(v.begin(), it, std::ostream_iterator<int>(std::cout));
   ```
 c) 
   ```cpp
    std::vector<int>::iterator it = std::unique(v.begin(), v.end()); 
    std::sort(v.begin(), it); 
    std::copy(v.begin(), it, std::ostream_iterator<int>(std::cout));
   ```
 d)
   ```cpp
     std::unique_copy(v.begin(), v.end(), std::ostream_iterator<int>(std::cout));
   ```
 e)
   ```cpp
     std::sort(v.begin(), v.end()); 
     std::unique_copy(v.begin(), v.end(), std::ostream_iterator<int>(std::cout));
   ```

26. Данный код являеться не коректным. Что будет выведенно на экране? и как сделать чтобы этот код был коректен
```cpp
#include <iostream>

class Test {
  public:
    explicit Test(int size_) {
      std::cout << "1" << std::endl;
    }
    Test(const Test& test) {
      std::cout << "2" << std::endl;
    }
    Test(Test&& test) {
      std::cout << "3" << std::endl;
    }
    Test& operator= (Test&& test) {
      std::cout << "4" << std::endl;
      return *this;
    }
    Test& operator= (const Test& test) {
      std::cout << "5" << std::endl;
      return *this;
    }
    ~Test() {
      std::cout << "6" << std::endl;
    }
};

Test process_data(size_t size_) {
  Test data(size_);
  return std::move(data);
}

int main() {
  auto data = process_data(100);
  return 0;
}
```