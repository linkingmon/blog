---
title: "Gdb skills - part 2"
date: 2026-03-17
tags: ["gdb"]
---

Keep sharing some gdb skills, more advanced.

## x.gdb
Writing the gdb commands inside the x.gdb file so that we don't need to spedify the commands every time.

## set logging on 
Usually the gdb outcome would not be stored, we can use `set loging on`, then the output would be stored in `gdb.txt`, this is useful when we need to print a lot of things, we can print them all in the log, then review the output later, or we can compare log in different runs.

## Python Pretty printer
Sometiems the class would be complicated and what we really care is some of the attributes only.

We can use python pretty print to print out the information we want.

Say we have this code.
```c++
#include <iostream>
#include <string>

class Fruit {
public:
    std::string name;
    int sweetness;

    Fruit(std::string n, int s) : name(n), sweetness(s) {}
};

int main() {
    Fruit f("Apple", 8);
    std::cout << "Program running\n";
    return 0;
}
```

We can write a python script as
```python
import gdb
import gdb.printing

class FruitPrinter:
    def __init__(self, val):
        self.val = val

    def to_string(self):
        name = self.val['name']['_M_dataplus']['_M_p'].string()
        sweetness = int(self.val['sweetness'])
        return f'Fruit(name="{name}", sweetness={sweetness})'

def build_pretty_printer():
    pp = gdb.printing.RegexpCollectionPrettyPrinter("fruit")
    pp.add_printer("Fruit", "^Fruit$", FruitPrinter)
    return pp

gdb.printing.register_pretty_printer(
    gdb.current_objfile(),
    build_pretty_printer()
)
```

in this case we can run the gdb script 
```gdb
b main
r
n 1
p f
so
p f
```
then we can get
```gdb
$1 = {name =  {_M_dataplus = {<std::allocator .....}, sweetness = 8}
$2 = Fruit(name = Apple, sweetness=8) 
```
