
Write this in signal.java file

*Signal.java*
```
package Signal;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;

public final class signal {

    List<Method> functions = new ArrayList<Method>();
    List<Object> objects = new ArrayList<Object>();
    public void connect(Object obj,String function)
    {
        try {
            functions.add(obj.getClass().getMethod(function));
            objects.add(obj);
        } catch (NoSuchMethodException e) {
            throw new RuntimeException(e);
        }
    }

    public void emit()
    {
        for(int i = 0;i<functions.size();i++)
        {
            try {
                functions.get(i).invoke(objects.get(i));
            } catch (IllegalAccessException e) {
                throw new RuntimeException(e);
            } catch (InvocationTargetException e) {
                throw new RuntimeException(e);
            }
        }
    }
}

```

below is a code snippet demonstrating how to use the class

*testclass.java*  file
```
public class testclass{

      public  void tesclassfunction1()
      {
          System.out.println("haha test clas function 1");
      }
}

```


*Main.java* file
```
import Signal;

public class Main {

    public void testfunction1()
    {
        System.out.println("haha test function 1");
    }

    public  void testfucntion2()
    {
        System.out.println("haha test fucntion 2");
    }
    public static void main(String[] args)
    {
        System.out.println("Hello world!");
        signal testsignal = new signal();
        testclass testobject = new testclass();
        testsignal.connect(testobject,"tesclassfunction1");
        testsignal.emit();
   
    }
};
```