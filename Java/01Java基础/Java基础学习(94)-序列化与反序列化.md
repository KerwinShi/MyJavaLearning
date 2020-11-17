序列化：把Java对象转换为字节序列，将数据分解成字节流，以便存储在文件中或在网络上传输  
反序列化：把字节序列恢复为Java对象，打开字节流并重构对象  

>作用:其好处一是实现了数据的持久化，通过序列化可以把数据永久地保存到硬盘上（通常存放在文件里），二是，利用序列化实现远程通信，即在网络上传送对象的字节序列。  

为了能够准确的进行序列化、反序列化，大家需要约定一种规则，就是协议。常见的协议有：  
XML&SOAP（Simple Object Access protocol）：XML 是一种常用的序列化和反序列化协议，具有跨机器，跨语言等优点，SOAP（Simple Object Access protocol） 是一种被广泛应用的，基于 XML 为序列化和反序列化协议的结构化消息传递协议  
[JSON](../05工具JSON/JSON速览.md)（Javascript Object Notation）  
Protobuf  

Java实现  
一、Serializable 接口  
=  
```java
public interface Serializable {
}
```  
>没有看错，他就是什么都没有，Serializable是一个空接口，就是一个标记，用来标识当前类可以被 ObjectOutputStream 序列化，以及被 ObjectInputStream 反序列化（[IO](./Java基础学习(93)-IO.md)）
需要进行序列化的类必须要实现接口Serializable，否则序列化过程会报错；在反序列化过程中，它的父类如果没有实现序列化接口，那么将需要提供无参构造函数来重新创建对象；一个实现 Serializable 接口的子类也是可以被序列化的；静态成员变量是不能被序列化；transient 标识的对象成员变量不参与序列化；Serializable 在序列化和反序列化过程中大量使用了反射，因此其过程会产生的大量的内存碎片。  

```java
private void writeObject(java.io.ObjectOutputStream s)throws java.io.IOException
private void readObject(java.io.ObjectInputStream s)throws java.io.IOException, ClassNotFoundException
```
序列化过程=关键所在，这两个方法分别是序列化写入数据和反序列化读取数据用的，可以在原来的基础上，进行修改，实现自定义的序列化与反序列化操作    

序列化ID  
在进行序列化时，加了一个serialVersionUID字段，这便是序列化ID  
>java的序列化机制是通过判断运行时类的serialVersionUID来验证版本一致性的，在进行反序列化时，JVM会把传进来的字节流中的serialVersionUID与本地实体类中的serialVersionUID进行比较，如果相同则认为是一致的，便可以进行反序列化，否则就会报序列化版本不一致的异常。  

 AS plugin插件、 JDK的bin目录下的serialver工具可以生成serialVersionUID  

 serialVersionUID的兼容性问题  
 ```
java.io.InvalidClassException: com.example.seriable.Student; local class incompatible: stream classdesc
serialVersionUID = -926212341182608815, local class serialVersionUID = -6840182814363029482
 ```
 >这个异常，它是属于兼容问题异常，是发生在反序列化阶段，检测到 serialVersionUID 不一致导致的  

 导致这个id变化的原因有：  
 手动去修改导致当前的 serialVersionUID 与序列化前的不一样。  
 没有手动去写这个 serialVersionUID 常量，那么 JVM 内部会根据类结构去计算得到这个 serialVersionUID 值，在类结构发生改变时(属性增加，删除或者类型修改了)这种也是会导致 serialVersionUID 发生变化。  
 类结构没有发生改变，并且没有定义 serialVersionUID ，但是反序列和序列化操作的虚拟机不一样也可能导致计算出来的 serialVersionUID 不一样。  

>JVM 规范强烈建议我们手动声明一个版本号，这个数字可以是随机的，只要固定不变就可以。同时最好是 private 和 final 的，尽量保证不变。   


二、Externalizable 接口    
=  
自定义序列化过程  
```java  
public interface Externalizable extends Serializable {
  	//将要序列化的对象属性通过 var1.wrietXxx() 写入到序列化流中
    void writeExternal(ObjectOutput var1) throws IOException;
		//将要反序列化的对象属性通过 var1.readXxx() 读出来
    void readExternal(ObjectInput var1) throws IOException, ClassNotFoundException;
}
```

小结一下，实现序列化的方法有：    
### 方法一：
若Student类仅仅实现了Serializable接口，则可以按照以下方式进行序列化和反序列化。  
ObjectOutputStream采用默认的序列化方式，对Student对象的非transient的实例变量进行序列化。  
ObjcetInputStream采用默认的反序列化方式，对对Student对象的非transient的实例变量进行反序列化。 

### 方法二：
若Student类仅仅实现了Serializable接口，并且还定义了readObject(ObjectInputStream in)和writeObject(ObjectOutputSteam out)，则采用以下方式进行序列化与反序列化。  
ObjectOutputStream调用Student对象的writeObject(ObjectOutputStream out)的方法进行序列化。  
ObjectInputStream会调用Student对象的readObject(ObjectInputStream in)的方法进行反序列化。  

### 方法三：
若Student类实现了Externalnalizable接口，且Student类必须实现readExternal(ObjectInput in)和writeExternal(ObjectOutput out)方法，则按照以下方式进行序列化与反序列化。  
ObjectOutputStream调用Student对象的writeExternal(ObjectOutput out))的方法进行序列化。  
ObjectInputStream会调用Student对象的readExternal(ObjectInput in)的方法进行反序列化。  


三、深入理解序列化过程
序列化与反序列化的过程大致上为：  
创建一个对象输出流，它可以包装一个其它类型的目标输出流，如文件输出流；通过对象输出流的writeObject()方法写对象。  
```java  
ObjectOutputStream out = new ObjectOutputStream(new fileOutputStream(“D:\\objectfile.obj”));
//写对象
out.writeObject(“Hello”);
out.writeObject(new Date());
```  
创建一个对象输入流，它可以包装一个其它类型输入流，如文件输入流；通过对象输出流的readObject()方法读取对象。  
```java
ObjectInputStream in = new ObjectInputStream(new fileInputStream(“D:\\objectfile.obj”));
String obj1 = (String)in.readObject();
Date obj2 = (Date)in.readObject();
```

序列化详细过程  
将对象实例相关的类元数据输出  
递归地输出类的超类描述直到不再有超类  
类元数据完了以后，开始从最顶层的超类开始输出对象实例的实际数据值  
从上至下递归输出实例的数据  

```java
//构造函数
public ObjectOutputStream(OutputStream out) throws IOException {
    verifySubclass();
    //用于写入一些类元数据还有对象中基本数据类型的值
    bout = new BlockDataOutputStream(out);
    handles = new HandleTable(10, (float) 3.00);
    subs = new ReplaceTable(10, (float) 3.00);
    //false 表示不支持重写序列化过程，如果为 true ，那么需要重写 writeObjectOverride 方法
    enableOverride = false;
    //写入头信息
    writeStreamHeader();
    bout.setBlockDataMode(true);
    if (extendedDebugInfo) {
        debugInfoStack = new DebugTraceInfoStack();
    } else {
        debugInfoStack = null;
    }
}

/**
* Magic number that is written to the stream header.
*/
//声明使用了序列化协议
final static short STREAM_MAGIC = (short)0xaced;

/**
* Version number that is written to the stream header.
*/
//指定序列化协议版本
final static short STREAM_VERSION = 5;
protected void writeStreamHeader() throws IOException {
    bout.writeShort(STREAM_MAGIC);
    bout.writeShort(STREAM_VERSION);
}


//关键的writeObject方法
public final void writeObject(Object obj) throws IOException {
    if (enableOverride) {
        //重写 writeObjectOverride 方法时执行
        writeObjectOverride(obj);
        return;
    }
    try {
        //默认执行
        writeObject0(obj, false);
    } catch (IOException ex) {
        if (depth == 0) {
            writeFatalException(ex);
        }
        throw ex;
    }
}



private void writeObject0(Object obj, boolean unshared)
    throws IOException
{
    boolean oldMode = bout.setBlockDataMode(false);
    depth++;
    try {
        // handle previously written and non-replaceable objects
        int h;
        if ((obj = subs.lookup(obj)) == null) {
            writeNull();
            return;
        } else if (!unshared && (h = handles.lookup(obj)) != -1) {
            writeHandle(h);
            return;
        } else if (obj instanceof Class) {
            writeClass((Class) obj, unshared);
            return;
        } else if (obj instanceof ObjectStreamClass) {
            writeClassDesc((ObjectStreamClass) obj, unshared);
            return;
        }

        // check for replacement object
        Object orig = obj;
        Class<?> cl = obj.getClass();
        ObjectStreamClass desc;
        for (;;) {
            // REMIND: skip this check for strings/arrays?
            Class<?> repCl;
            //查找当前类的 ObjectStreamClass ，它是用于描述一个类的结构信息的，通过它就可以获取对象及其对象属性的相关信息，并且它内部持有该对象的父类的 ObjectStreamClass 实例
            desc = ObjectStreamClass.lookup(cl, true);
            if (!desc.hasWriteReplaceMethod() ||
                (obj = desc.invokeWriteReplace(obj)) == null ||
                (repCl = obj.getClass()) == cl)
            {
                break;
            }
            cl = repCl;
        }
        if (enableReplace) {
            Object rep = replaceObject(obj);
            if (rep != obj && rep != null) {
                cl = rep.getClass();
                desc = ObjectStreamClass.lookup(cl, true);
            }
            obj = rep;
        }

        // if object replaced, run through original checks a second time
        if (obj != orig) {
            subs.assign(orig, obj);
            if (obj == null) {
                writeNull();
                return;
            } else if (!unshared && (h = handles.lookup(obj)) != -1) {
                writeHandle(h);
                return;
            } else if (obj instanceof Class) {
                writeClass((Class) obj, unshared);
                return;
            } else if (obj instanceof ObjectStreamClass) {
                writeClassDesc((ObjectStreamClass) obj, unshared);
                return;
            }
        }

        // remaining cases
        //根据 obj 的类型去执行序列化操作
        if (obj instanceof String) {
            writeString((String) obj, unshared);
        } else if (cl.isArray()) {
            writeArray(obj, desc, unshared);
        } else if (obj instanceof Enum) {
            writeEnum((Enum<?>) obj, desc, unshared);
        } else if (obj instanceof Serializable) {
            writeOrdinaryObject(obj, desc, unshared);
        } else {
            //如果不符合序列化要求，那么会置抛出 NotSerializableException 异常。
            if (extendedDebugInfo) {
                throw new NotSerializableException(
                    cl.getName() + "\n" + debugInfoStack.toString());
            } else {
                throw new NotSerializableException(cl.getName());
            }
        }
    } finally {
        depth--;
        bout.setBlockDataMode(oldMode);
    }
}



private void writeOrdinaryObject(Object obj,
                                    ObjectStreamClass desc,
                                    boolean unshared)
    throws IOException
{
    if (extendedDebugInfo) {
        debugInfoStack.push(
            (depth == 1 ? "root " : "") + "object (class \"" +
            obj.getClass().getName() + "\", " + obj.toString() + ")");
    }
    try {
        desc.checkSerialize();
        //写入类的元数据
        bout.writeByte(TC_OBJECT);
        //自上而下(从父类写到子类，注意只会遍历那些实现了序列化接口的类)写入描述信息
        writeClassDesc(desc, false);
        handles.assign(unshared ? null : obj);
        //判断需要序列化的对象是否实现了 Externalizable 接口
        if (desc.isExternalizable() && !desc.isProxy()) {
            writeExternalData((Externalizable) obj);
        } else {
            //writeSerialData 在没有实现 Externalizable 接口时，就执行这个方法
            writeSerialData(obj, desc);
        }
    } finally {
        if (extendedDebugInfo) {
            debugInfoStack.pop();
        }
    }
}


private void writeSerialData(Object obj, ObjectStreamClass desc)
    throws IOException
{
    //用于返回序列化对象及其父类的 ClassDataSlot[] 数组
    ObjectStreamClass.ClassDataSlot[] slots = desc.getClassDataLayout();
    for (int i = 0; i < slots.length; i++) {
        ObjectStreamClass slotDesc = slots[i].desc;
        //要序列化这个对象是否有 writeObject 这个 private 方法
        if (slotDesc.hasWriteObjectMethod()) {
            PutFieldImpl oldPut = curPut;
            curPut = null;
            SerialCallbackContext oldContext = curContext;

            if (extendedDebugInfo) {
                debugInfoStack.push(
                    "custom writeObject data (class \"" +
                    slotDesc.getName() + "\")");
            }
            try {
                curContext = new SerialCallbackContext(obj, slotDesc);
                bout.setBlockDataMode(true);
                slotDesc.invokeWriteObject(obj, this);
                bout.setBlockDataMode(false);
                bout.writeByte(TC_ENDBLOCKDATA);
            } finally {
                curContext.setUsed();
                curContext = oldContext;
                if (extendedDebugInfo) {
                    debugInfoStack.pop();
                }
            }

            curPut = oldPut;
        } else {
            //VM 自动帮我们序列化了
            //写入基本数据类型的数据;写入引用数据类型的数据，这里最终又会调用到writeObject0() 方法
            defaultWriteFields(obj, slotDesc);
        }
    }
}
```




参考：  
1.https://blog.csdn.net/mocas_wang/article/details/107621010  
2.









