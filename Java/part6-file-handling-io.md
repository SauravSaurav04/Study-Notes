# Part 6: File Handling & I/O

**Focus Areas:** File Operations, Streams, Serialization, I/O Operations

---

## 📋 Table of Contents
1. [Introduction to File Handling](#-introduction-to-file-handling)
2. [Byte Streams vs Character Streams](#-byte-streams-vs-character-streams)
3. [File Class](#-file-class)
4. [Reader and Writer Classes](#-reader-and-writer-classes)
5. [BufferedReader and BufferedWriter](#-bufferedreader-and-bufferedwriter)
6. [Serialization](#-serialization)
7. [Deserialization](#-deserialization)
8. [NIO (New I/O) Package](#-nio-new-io-package)
9. [📄 Cheatsheet](#-cheatsheet)

---

## 🎯 Introduction to File Handling

**Theory:**
File handling in Java allows us to create, read, update, and delete files stored on the disk. Java provides extensive I/O (Input/Output) APIs in the `java.io` package.

### Why File Handling?

1. ✅ **Data Persistence**: Store data permanently on disk
2. ✅ **Data Exchange**: Share data between applications
3. ✅ **Logging**: Keep track of application events
4. ✅ **Configuration**: Store application settings
5. ✅ **Backup**: Save important information

### Java I/O Architecture

```
java.io Package
    ├── Byte Streams (Binary Data)
    │   ├── InputStream (Abstract)
    │   │   ├── FileInputStream
    │   │   ├── BufferedInputStream
    │   │   └── ObjectInputStream
    │   └── OutputStream (Abstract)
    │       ├── FileOutputStream
    │       ├── BufferedOutputStream
    │       └── ObjectOutputStream
    │
    └── Character Streams (Text Data)
        ├── Reader (Abstract)
        │   ├── FileReader
        │   ├── BufferedReader
        │   └── InputStreamReader
        └── Writer (Abstract)
            ├── FileWriter
            ├── BufferedWriter
            └── OutputStreamWriter
```

### Stream vs Reader/Writer

| Aspect | Streams (Byte) | Reader/Writer (Character) |
|--------|----------------|---------------------------|
| **Data Type** | Binary (bytes) | Text (characters) |
| **Size** | 8-bit (1 byte) | 16-bit (2 bytes) |
| **Use For** | Images, videos, audio | Text files, logs |
| **Classes** | InputStream, OutputStream | Reader, Writer |
| **Example** | FileInputStream, FileOutputStream | FileReader, FileWriter |

---

## 🎯 Byte Streams vs Character Streams

**Theory:**
Java provides two types of streams for handling input and output operations: Byte Streams and Character Streams.

### Byte Streams

**Definition:**
- Handle **binary data** (images, videos, audio, etc.)
- Process data **8 bits (1 byte) at a time**
- Base classes: **InputStream** and **OutputStream**
- Used for **all types of files**

**Key Classes:**
- **FileInputStream**: Read bytes from a file
- **FileOutputStream**: Write bytes to a file
- **BufferedInputStream**: Buffered input for efficiency
- **BufferedOutputStream**: Buffered output for efficiency
- **ObjectInputStream**: Read objects from a file
- **ObjectOutputStream**: Write objects to a file

**Example:**
```java
import java.io.*;

public class ByteStreamExample {
    public static void main(String[] args) {
        // Write bytes to file
        try (FileOutputStream fos = new FileOutputStream("data.bin")) {
            String data = "Hello, Byte Streams!";
            byte[] bytes = data.getBytes();
            fos.write(bytes);
            System.out.println("Data written successfully");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Read bytes from file
        try (FileInputStream fis = new FileInputStream("data.bin")) {
            int content;
            while ((content = fis.read()) != -1) {
                System.out.print((char) content);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Data written successfully
Hello, Byte Streams!
```

---

### Character Streams

**Definition:**
- Handle **text data** (characters)
- Process data **16 bits (2 bytes) at a time** (Unicode)
- Base classes: **Reader** and **Writer**
- Automatically handle **character encoding**
- More efficient for **text files**

**Key Classes:**
- **FileReader**: Read characters from a file
- **FileWriter**: Write characters to a file
- **BufferedReader**: Buffered character input
- **BufferedWriter**: Buffered character output
- **InputStreamReader**: Byte stream to character stream bridge
- **OutputStreamWriter**: Character stream to byte stream bridge

**Example:**
```java
import java.io.*;

public class CharacterStreamExample {
    public static void main(String[] args) {
        // Write characters to file
        try (FileWriter fw = new FileWriter("data.txt")) {
            fw.write("Hello, Character Streams!\n");
            fw.write("Java File Handling is easy.");
            System.out.println("Data written successfully");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Read characters from file
        try (FileReader fr = new FileReader("data.txt")) {
            int content;
            while ((content = fr.read()) != -1) {
                System.out.print((char) content);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Data written successfully
Hello, Character Streams!
Java File Handling is easy.
```

---

### Byte Streams vs Character Streams: Comparison

| Feature | Byte Streams | Character Streams |
|---------|--------------|-------------------|
| **Data Type** | Binary (bytes) | Text (characters) |
| **Size** | 8-bit (1 byte) | 16-bit (2 bytes) |
| **Base Classes** | InputStream, OutputStream | Reader, Writer |
| **Use Case** | Images, videos, audio, binary files | Text files, logs, configuration |
| **Encoding** | No automatic encoding | Handles character encoding (UTF-8, etc.) |
| **Performance** | Faster for binary data | Faster for text data |
| **Examples** | FileInputStream, FileOutputStream | FileReader, FileWriter |
| **Suffix** | Stream | Reader/Writer |

**When to Use:**

| Scenario | Use |
|----------|-----|
| Reading/Writing text files | **Character Streams** (FileReader, FileWriter) |
| Reading/Writing images, videos | **Byte Streams** (FileInputStream, FileOutputStream) |
| Object serialization | **Byte Streams** (ObjectInputStream, ObjectOutputStream) |
| Large text files | **Buffered Character Streams** (BufferedReader, BufferedWriter) |
| Network communication | **Byte Streams** |
| Configuration files | **Character Streams** |

---

## 🎯 File Class

**Theory:**
The `File` class from `java.io` package represents a **file or directory path**. It provides methods to perform various operations like creating, deleting, and checking file properties.

**Important:** File class does NOT actually read/write file content. It only manages file/directory metadata and paths.

### File Class Constructors

```java
// Constructor 1: Using file path
File file1 = new File("test.txt");

// Constructor 2: Using directory and file name
File file2 = new File("C:\\Users\\Documents", "test.txt");

// Constructor 3: Using parent File object and child file name
File dir = new File("C:\\Users\\Documents");
File file3 = new File(dir, "test.txt");
```

### File Class Methods

#### 1. File Creation and Deletion

```java
import java.io.*;

public class FileCreationDemo {
    public static void main(String[] args) {
        try {
            // Create a new file
            File file = new File("example.txt");
            
            if (file.createNewFile()) {
                System.out.println("File created: " + file.getName());
            } else {
                System.out.println("File already exists");
            }
            
            // Create a directory
            File dir = new File("myFolder");
            if (dir.mkdir()) {
                System.out.println("Directory created: " + dir.getName());
            }
            
            // Create multiple directories
            File multiDir = new File("parent/child/grandchild");
            if (multiDir.mkdirs()) {
                System.out.println("Multiple directories created");
            }
            
            // Delete a file
            if (file.delete()) {
                System.out.println("File deleted successfully");
            }
            
        } catch (IOException e) {
            System.out.println("An error occurred: " + e.getMessage());
        }
    }
}
```

#### 2. File Information Methods

```java
import java.io.*;

public class FileInfoDemo {
    public static void main(String[] args) {
        File file = new File("example.txt");
        
        if (file.exists()) {
            System.out.println("File Name: " + file.getName());
            System.out.println("Absolute Path: " + file.getAbsolutePath());
            System.out.println("Parent Directory: " + file.getParent());
            System.out.println("File Size: " + file.length() + " bytes");
            System.out.println("Can Read: " + file.canRead());
            System.out.println("Can Write: " + file.canWrite());
            System.out.println("Can Execute: " + file.canExecute());
            System.out.println("Is Directory: " + file.isDirectory());
            System.out.println("Is File: " + file.isFile());
            System.out.println("Is Hidden: " + file.isHidden());
            System.out.println("Last Modified: " + file.lastModified());
        } else {
            System.out.println("File does not exist");
        }
    }
}
```

#### 3. Listing Directory Contents

```java
import java.io.File;

public class DirectoryListingDemo {
    public static void main(String[] args) {
        File dir = new File("C:\\Users\\Documents");
        
        if (dir.isDirectory()) {
            // Method 1: list() - returns String array of names
            String[] fileNames = dir.list();
            System.out.println("Files and Directories:");
            for (String name : fileNames) {
                System.out.println(name);
            }
            
            System.out.println("\n---\n");
            
            // Method 2: listFiles() - returns File array
            File[] files = dir.listFiles();
            for (File file : files) {
                if (file.isDirectory()) {
                    System.out.println("[DIR] " + file.getName());
                } else {
                    System.out.println("[FILE] " + file.getName() + 
                                     " (" + file.length() + " bytes)");
                }
            }
        }
    }
}
```

#### 4. File Filtering

```java
import java.io.*;

public class FileFilterDemo {
    public static void main(String[] args) {
        File dir = new File("C:\\Users\\Documents");
        
        // Filter only .txt files
        FilenameFilter txtFilter = new FilenameFilter() {
            public boolean accept(File dir, String name) {
                return name.toLowerCase().endsWith(".txt");
            }
        };
        
        File[] txtFiles = dir.listFiles(txtFilter);
        System.out.println("Text Files:");
        for (File file : txtFiles) {
            System.out.println(file.getName());
        }
        
        // Using lambda (Java 8+)
        File[] pdfFiles = dir.listFiles((d, name) -> name.endsWith(".pdf"));
        System.out.println("\nPDF Files:");
        for (File file : pdfFiles) {
            System.out.println(file.getName());
        }
    }
}
```

### Important File Class Methods Summary

| Method | Description | Return Type |
|--------|-------------|-------------|
| `createNewFile()` | Creates new empty file | boolean |
| `mkdir()` | Creates single directory | boolean |
| `mkdirs()` | Creates multiple directories | boolean |
| `delete()` | Deletes file or empty directory | boolean |
| `exists()` | Checks if file exists | boolean |
| `getName()` | Gets file name | String |
| `getAbsolutePath()` | Gets absolute path | String |
| `getParent()` | Gets parent directory | String |
| `length()` | Gets file size in bytes | long |
| `canRead()` | Checks read permission | boolean |
| `canWrite()` | Checks write permission | boolean |
| `isDirectory()` | Checks if it's a directory | boolean |
| `isFile()` | Checks if it's a file | boolean |
| `list()` | Lists names in directory | String[] |
| `listFiles()` | Lists File objects in directory | File[] |
| `lastModified()` | Gets last modified time | long |
| `renameTo(File)` | Renames/moves file | boolean |

---

## 🎯 Reader and Writer Classes

**Theory:**
Reader and Writer are abstract base classes for reading and writing **character streams**. They handle character encoding and are ideal for text files.

### FileReader and FileWriter

#### FileWriter - Writing to Files

**Example 1: Basic FileWriter**
```java
import java.io.*;

public class FileWriterDemo {
    public static void main(String[] args) {
        // Write to file (overwrites existing content)
        try (FileWriter writer = new FileWriter("output.txt")) {
            writer.write("Hello, World!\n");
            writer.write("Java File Handling\n");
            writer.write("Learning I/O Operations");
            System.out.println("Data written successfully");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Example 2: Append Mode**
```java
import java.io.*;

public class FileWriterAppendDemo {
    public static void main(String[] args) {
        // Append to file (preserves existing content)
        try (FileWriter writer = new FileWriter("output.txt", true)) {
            writer.write("\nAppended Line 1");
            writer.write("\nAppended Line 2");
            System.out.println("Data appended successfully");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### FileReader - Reading from Files

**Example 1: Reading Character by Character**
```java
import java.io.*;

public class FileReaderDemo {
    public static void main(String[] args) {
        try (FileReader reader = new FileReader("output.txt")) {
            int character;
            while ((character = reader.read()) != -1) {
                System.out.print((char) character);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Example 2: Reading into Character Array**
```java
import java.io.*;

public class FileReaderArrayDemo {
    public static void main(String[] args) {
        try (FileReader reader = new FileReader("output.txt")) {
            char[] buffer = new char[100];
            int charsRead = reader.read(buffer);
            
            System.out.println("Characters read: " + charsRead);
            System.out.println("Content: " + new String(buffer, 0, charsRead));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 🎯 BufferedReader and BufferedWriter

**Theory:**
Buffered classes provide **buffering** to character input/output streams. They read/write data in chunks (buffer) rather than one character at a time, which significantly improves performance.

### Why Use Buffered Classes?

1. ✅ **Performance**: Reduces number of I/O operations (faster)
2. ✅ **Efficiency**: Reads/writes data in chunks (8192 bytes default)
3. ✅ **Convenience**: Provides `readLine()` method
4. ✅ **Less System Calls**: Fewer disk access operations

### BufferedWriter - Efficient Writing

```java
import java.io.*;

public class BufferedWriterDemo {
    public static void main(String[] args) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter("buffered.txt"))) {
            writer.write("Line 1: Hello, BufferedWriter!");
            writer.newLine();  // Platform-independent newline
            
            writer.write("Line 2: Java I/O");
            writer.newLine();
            
            writer.write("Line 3: Buffering improves performance");
            writer.newLine();
            
            // Write multiple lines
            String[] lines = {"Line 4", "Line 5", "Line 6"};
            for (String line : lines) {
                writer.write(line);
                writer.newLine();
            }
            
            System.out.println("Data written successfully with buffering");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### BufferedReader - Efficient Reading

**Example 1: Reading Line by Line**
```java
import java.io.*;

public class BufferedReaderDemo {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("buffered.txt"))) {
            String line;
            int lineNumber = 1;
            
            while ((line = reader.readLine()) != null) {
                System.out.println(lineNumber + ": " + line);
                lineNumber++;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Example 2: Reading from Console**
```java
import java.io.*;

public class ConsoleInputDemo {
    public static void main(String[] args) {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        
        try {
            System.out.print("Enter your name: ");
            String name = reader.readLine();
            
            System.out.print("Enter your age: ");
            int age = Integer.parseInt(reader.readLine());
            
            System.out.println("\nHello, " + name + "! You are " + age + " years old.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Example 3: Reading Entire File Content**
```java
import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class ReadAllLinesDemo {
    public static void main(String[] args) {
        List<String> lines = new ArrayList<>();
        
        try (BufferedReader reader = new BufferedReader(new FileReader("buffered.txt"))) {
            String line;
            while ((line = reader.readLine()) != null) {
                lines.add(line);
            }
            
            System.out.println("Total lines: " + lines.size());
            System.out.println("All lines: " + lines);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Performance Comparison

```java
import java.io.*;

public class PerformanceComparison {
    public static void main(String[] args) {
        String filename = "large_file.txt";
        int lines = 100000;
        
        // Write large file for testing
        writeTestFile(filename, lines);
        
        // Test FileReader (no buffering)
        long start = System.currentTimeMillis();
        readWithFileReader(filename);
        long fileReaderTime = System.currentTimeMillis() - start;
        
        // Test BufferedReader (with buffering)
        start = System.currentTimeMillis();
        readWithBufferedReader(filename);
        long bufferedReaderTime = System.currentTimeMillis() - start;
        
        System.out.println("FileReader time: " + fileReaderTime + " ms");
        System.out.println("BufferedReader time: " + bufferedReaderTime + " ms");
        System.out.println("Speed improvement: " + 
                         (fileReaderTime / bufferedReaderTime) + "x faster");
    }
    
    private static void writeTestFile(String filename, int lines) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filename))) {
            for (int i = 1; i <= lines; i++) {
                writer.write("This is line number " + i);
                writer.newLine();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    private static void readWithFileReader(String filename) {
        try (FileReader reader = new FileReader(filename)) {
            int ch;
            while ((ch = reader.read()) != -1) {
                // Just reading, not processing
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    private static void readWithBufferedReader(String filename) {
        try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
            String line;
            while ((line = reader.readLine()) != null) {
                // Just reading, not processing
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Typical Output:**
```
FileReader time: 450 ms
BufferedReader time: 45 ms
Speed improvement: 10x faster
```

### BufferedReader vs FileReader

| Feature | FileReader | BufferedReader |
|---------|------------|----------------|
| **Performance** | Slow (one char at a time) | Fast (buffered) |
| **Buffer** | No internal buffer | 8192 chars buffer |
| **readLine()** | ❌ Not available | ✅ Available |
| **Use Case** | Small files | Large files |
| **Efficiency** | Low | High |
| **System Calls** | More (per character) | Less (per buffer) |

### 🔥 Key Differences Explained

**1. Performance:**
- **FileReader**: Reads **one character at a time** from the disk, making a separate system call for each character. This is extremely slow for large files.
- **BufferedReader**: Reads a **large chunk (8192 characters)** into memory at once, then serves characters from the buffer. This dramatically reduces disk I/O operations.

**2. Convenience:**
- **FileReader**: Only provides basic `read()` methods for reading individual characters or character arrays.
- **BufferedReader**: Provides the convenient **`readLine()`** method to read entire lines of text, making it much easier to process text files.

**3. System Calls:**
- **FileReader**: Makes a system call **for every character** (very expensive).
- **BufferedReader**: Makes one system call **per buffer** (8192 chars), then serves from memory.

### Usage Comparison

**FileReader - Basic Reading:**
```java
// FileReader reads character by character (SLOW)
try (FileReader reader = new FileReader("file.txt")) {
    int ch;
    while ((ch = reader.read()) != -1) {
        System.out.print((char) ch);  // Each character requires disk access
    }
}
```

**BufferedReader - Efficient Reading:**
```java
// BufferedReader reads in chunks and provides readLine() (FAST)
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);  // Reads from buffer in memory
    }
}
```

### ✅ Best Practice

**Always wrap FileReader with BufferedReader for better performance:**

```java
// ❌ BAD - Direct FileReader (slow)
try (FileReader reader = new FileReader("large_file.txt")) {
    int ch;
    while ((ch = reader.read()) != -1) {
        // Slow for large files
    }
}

// ✅ GOOD - BufferedReader wrapping FileReader (fast)
try (BufferedReader reader = new BufferedReader(new FileReader("large_file.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        // Much faster, especially for large files
    }
}
```

### Performance Impact

**Real-world Impact:**
- For a **10,000 line file**: BufferedReader can be **10-100x faster** than FileReader
- For a **100,000 line file**: The difference becomes even more dramatic
- **Why?** Disk I/O is expensive. Reading 8192 characters at once is much faster than making 8192 individual read calls.

**Memory vs Speed Trade-off:**
- BufferedReader uses **~16 KB of memory** for the buffer
- This small memory cost provides **massive speed improvements**
- For modern systems, this is an excellent trade-off

### When to Use Each

| Scenario | Use | Reason |
|----------|-----|--------|
| Reading text files | **BufferedReader** | Fast, convenient readLine() |
| Small configuration files (<100 chars) | FileReader acceptable | Minimal performance difference |
| Large files (>1000 lines) | **BufferedReader** | Significant performance gain |
| Processing line by line | **BufferedReader** | Has readLine() method |
| Reading char by char for parsing | BufferedReader | Still faster due to buffering |
| Production code | **BufferedReader** | Always the better choice |

**💡 Pro Tip:** Even if you don't need `readLine()`, wrap FileReader with BufferedReader for the performance benefits!

---

## 🎯 Serialization

**Theory:**
Serialization is the process of converting an **object into a byte stream** so that it can be saved to a file, sent over a network, or stored in a database. The reverse process is called **Deserialization**.

### Why Serialization?

1. ✅ **Persistence**: Save object state to disk
2. ✅ **Network Transfer**: Send objects over network (RMI, web services)
3. ✅ **Caching**: Store objects in memory or disk cache
4. ✅ **Deep Copy**: Create exact copy of objects
5. ✅ **Session Management**: Store user sessions

### How Serialization Works

```
Object (Memory) → Serialization → Byte Stream → File/Network
              ← Deserialization ←
```

### Serialization Requirements

1. ✅ Class must implement **`java.io.Serializable`** interface (marker interface)
2. ✅ All instance variables must be serializable
3. ✅ Use **`transient`** keyword for fields you don't want to serialize
4. ✅ Static fields are NOT serialized (they belong to class, not object)

### Serializable Interface

```java
public interface Serializable {
    // Empty interface (marker interface)
    // No methods to implement
}
```

### Basic Serialization Example

**Step 1: Create Serializable Class**
```java
import java.io.Serializable;

public class Student implements Serializable {
    // serialVersionUID (optional but recommended)
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    private String course;
    private transient String password;  // Will NOT be serialized
    private static String university = "XYZ University";  // Will NOT be serialized
    
    public Student(String name, int age, String course, String password) {
        this.name = name;
        this.age = age;
        this.course = course;
        this.password = password;
    }
    
    @Override
    public String toString() {
        return "Student{name='" + name + "', age=" + age + 
               ", course='" + course + "', password='" + password + 
               "', university='" + university + "'}";
    }
    
    // Getters and setters
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getCourse() { return course; }
}
```

**Step 2: Serialize Object to File**
```java
import java.io.*;

public class SerializationDemo {
    public static void main(String[] args) {
        Student student = new Student("John Doe", 22, "Computer Science", "secret123");
        
        // Serialize object
        try (FileOutputStream fileOut = new FileOutputStream("student.ser");
             ObjectOutputStream out = new ObjectOutputStream(fileOut)) {
            
            out.writeObject(student);
            System.out.println("Object serialized successfully");
            System.out.println("Saved: " + student);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Object serialized successfully
Saved: Student{name='John Doe', age=22, course='Computer Science', 
               password='secret123', university='XYZ University'}
```

---

## 🎯 Deserialization

**Theory:**
Deserialization is the process of **reconstructing an object from a byte stream**. It's the reverse process of serialization.

### Deserialization Example

```java
import java.io.*;

public class DeserializationDemo {
    public static void main(String[] args) {
        Student student = null;
        
        // Deserialize object
        try (FileInputStream fileIn = new FileInputStream("student.ser");
             ObjectInputStream in = new ObjectInputStream(fileIn)) {
            
            student = (Student) in.readObject();
            System.out.println("Object deserialized successfully");
            System.out.println("Loaded: " + student);
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Object deserialized successfully
Loaded: Student{name='John Doe', age=22, course='Computer Science', 
                password='null', university='XYZ University'}
```

**Note:** 
- `password` is `null` because it was marked as `transient`
- Static field `university` retains its current value (not from serialized data)

---

### Complete Serialization Example with Multiple Objects

```java
import java.io.*;
import java.util.ArrayList;
import java.util.List;

// Serializable class
class Employee implements Serializable {
    private static final long serialVersionUID = 1L;
    
    private int id;
    private String name;
    private double salary;
    private transient String ssn;  // Sensitive data - don't serialize
    
    public Employee(int id, String name, double salary, String ssn) {
        this.id = id;
        this.name = name;
        this.salary = salary;
        this.ssn = ssn;
    }
    
    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + 
               "', salary=" + salary + ", ssn='" + ssn + "'}";
    }
}

public class MultipleObjectsSerialization {
    public static void main(String[] args) {
        // Create list of employees
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee(101, "Alice", 50000, "123-45-6789"));
        employees.add(new Employee(102, "Bob", 60000, "987-65-4321"));
        employees.add(new Employee(103, "Charlie", 55000, "456-78-9012"));
        
        // Serialize list
        try (ObjectOutputStream out = new ObjectOutputStream(
                new FileOutputStream("employees.ser"))) {
            out.writeObject(employees);
            System.out.println("Employee list serialized successfully");
            System.out.println("Original list: " + employees);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Deserialize list
        try (ObjectInputStream in = new ObjectInputStream(
                new FileInputStream("employees.ser"))) {
            @SuppressWarnings("unchecked")
            List<Employee> loadedEmployees = (List<Employee>) in.readObject();
            System.out.println("\nEmployee list deserialized successfully");
            System.out.println("Loaded list: " + loadedEmployees);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

---

### serialVersionUID

**Theory:**
`serialVersionUID` is a unique identifier for each Serializable class. During deserialization, JVM verifies that the sender and receiver have compatible classes.

**Why serialVersionUID?**
- ✅ Version control for serialized objects
- ✅ Prevents `InvalidClassException` during deserialization
- ✅ Explicitly define class version
- ❌ Without it, JVM generates one (can cause issues)

**Example:**
```java
import java.io.Serializable;

public class Person implements Serializable {
    // Explicitly define serialVersionUID
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    
    // If you modify class (add field), increment serialVersionUID
    // private static final long serialVersionUID = 2L;
    
    // Constructors, methods...
}
```

**What Happens Without serialVersionUID?**
```java
// Original class
class Product implements Serializable {
    private String name;
    private double price;
}

// After serialization, you modify the class
class Product implements Serializable {
    private String name;
    private double price;
    private String category;  // New field added
}

// During deserialization:
// Exception: java.io.InvalidClassException: 
// local class incompatible: stream classdesc serialVersionUID = 1234,
// local class serialVersionUID = 5678
```

---

### Transient Keyword

**Theory:**
The `transient` keyword prevents specific fields from being serialized. Useful for sensitive or derived data.

**Use Cases:**
1. ✅ **Sensitive Data**: Passwords, credit card numbers, SSN
2. ✅ **Derived Data**: Calculated fields (can be recomputed)
3. ✅ **Non-serializable Fields**: References to non-serializable objects
4. ✅ **Large Data**: Data that can be fetched again (cache)

**Example:**
```java
import java.io.*;

class BankAccount implements Serializable {
    private static final long serialVersionUID = 1L;
    
    private String accountNumber;
    private String accountHolder;
    private double balance;
    private transient String pin;  // Don't serialize PIN
    private transient double interestRate;  // Can be recalculated
    
    public BankAccount(String accountNumber, String accountHolder, 
                      double balance, String pin) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = balance;
        this.pin = pin;
        this.interestRate = 0.05;  // 5%
    }
    
    @Override
    public String toString() {
        return "BankAccount{accountNumber='" + accountNumber + 
               "', accountHolder='" + accountHolder + 
               "', balance=" + balance + 
               ", pin='" + pin + 
               "', interestRate=" + interestRate + "}";
    }
}

public class TransientDemo {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("1234567890", "John Doe", 10000, "9876");
        
        // Serialize
        try (ObjectOutputStream out = new ObjectOutputStream(
                new FileOutputStream("account.ser"))) {
            out.writeObject(account);
            System.out.println("Serialized: " + account);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Deserialize
        try (ObjectInputStream in = new ObjectInputStream(
                new FileInputStream("account.ser"))) {
            BankAccount loadedAccount = (BankAccount) in.readObject();
            System.out.println("Deserialized: " + loadedAccount);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Serialized: BankAccount{accountNumber='1234567890', accountHolder='John Doe', 
                        balance=10000.0, pin='9876', interestRate=0.05}
Deserialized: BankAccount{accountNumber='1234567890', accountHolder='John Doe', 
                          balance=10000.0, pin='null', interestRate=0.0}
```

**Note:** `pin` and `interestRate` are `null`/`0.0` after deserialization because they're marked as `transient`.

---

### Inheritance and Serialization

**Scenario 1: Parent implements Serializable**
```java
import java.io.*;

class Parent implements Serializable {
    int parentData = 100;
}

class Child extends Parent {
    int childData = 200;
}

public class InheritanceSerializationDemo1 {
    public static void main(String[] args) {
        Child child = new Child();
        
        // Serialize
        try (ObjectOutputStream out = new ObjectOutputStream(
                new FileOutputStream("child.ser"))) {
            out.writeObject(child);
            System.out.println("Serialized: " + child.parentData + ", " + child.childData);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Deserialize
        try (ObjectInputStream in = new ObjectInputStream(
                new FileInputStream("child.ser"))) {
            Child loadedChild = (Child) in.readObject();
            System.out.println("Deserialized: " + loadedChild.parentData + 
                             ", " + loadedChild.childData);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Serialized: 100, 200
Deserialized: 100, 200
```

**✅ Both parent and child fields are serialized.**

---

**Scenario 2: Parent does NOT implement Serializable**
```java
import java.io.*;

class Parent {
    int parentData = 100;
    
    public Parent() {
        System.out.println("Parent constructor called");
    }
}

class Child extends Parent implements Serializable {
    int childData = 200;
    
    public Child() {
        System.out.println("Child constructor called");
    }
}

public class InheritanceSerializationDemo2 {
    public static void main(String[] args) {
        Child child = new Child();
        System.out.println("Before serialization: " + child.parentData + 
                         ", " + child.childData);
        
        // Serialize
        try (ObjectOutputStream out = new ObjectOutputStream(
                new FileOutputStream("child2.ser"))) {
            out.writeObject(child);
            System.out.println("\nSerialization completed");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Deserialize
        try (ObjectInputStream in = new ObjectInputStream(
                new FileInputStream("child2.ser"))) {
            Child loadedChild = (Child) in.readObject();
            System.out.println("\nAfter deserialization: " + loadedChild.parentData + 
                             ", " + loadedChild.childData);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Parent constructor called
Child constructor called
Before serialization: 100, 200

Serialization completed
Parent constructor called

After deserialization: 100, 200
```

**Notes:**
- ✅ Child fields are serialized
- ❌ Parent fields are NOT serialized
- ✅ Parent constructor is called during deserialization
- ❌ Child constructor is NOT called during deserialization

---

## 🎯 NIO (New I/O) Package

**Theory:**
Java NIO (New I/O) was introduced in Java 1.4 to provide better performance and additional features compared to traditional I/O. Java 7 introduced NIO.2 with enhanced file operations.

### NIO vs Traditional I/O

| Feature | Traditional I/O (java.io) | NIO (java.nio) |
|---------|---------------------------|----------------|
| **Approach** | Stream-oriented | Buffer-oriented |
| **Blocking** | Blocking I/O | Non-blocking I/O |
| **Performance** | Slower | Faster |
| **Channels** | No | Yes |
| **Selectors** | No | Yes (for multiplexing) |
| **Use Case** | Small files, simple operations | Large files, high performance |

### Key NIO Classes

1. **Path**: Represents file system path
2. **Paths**: Factory for creating Path objects
3. **Files**: Static methods for file operations
4. **FileSystem**: Interface to file system

### Path and Paths

```java
import java.nio.file.*;

public class PathDemo {
    public static void main(String[] args) {
        // Create Path objects
        Path path1 = Paths.get("test.txt");
        Path path2 = Paths.get("C:", "Users", "Documents", "file.txt");
        Path path3 = Paths.get("C:/Users/Documents/file.txt");
        
        System.out.println("Path: " + path1);
        System.out.println("Absolute Path: " + path1.toAbsolutePath());
        System.out.println("File Name: " + path1.getFileName());
        System.out.println("Parent: " + path1.getParent());
        System.out.println("Root: " + path1.getRoot());
        
        // Path operations
        Path path4 = Paths.get("folder/subfolder/../file.txt");
        System.out.println("Normalized: " + path4.normalize());
    }
}
```

### Files Class - Common Operations

**Example 1: Reading and Writing**
```java
import java.nio.file.*;
import java.io.IOException;
import java.util.List;

public class FilesDemo {
    public static void main(String[] args) {
        Path path = Paths.get("nio_example.txt");
        
        try {
            // Write to file
            String content = "Hello, NIO!\nJava File Handling\nLine 3";
            Files.write(path, content.getBytes());
            System.out.println("File written successfully");
            
            // Read entire file as byte array
            byte[] bytes = Files.readAllBytes(path);
            System.out.println("\nContent (bytes): " + new String(bytes));
            
            // Read all lines as List
            List<String> lines = Files.readAllLines(path);
            System.out.println("\nLines: " + lines);
            
            // Read line by line (Java 8+)
            System.out.println("\nLine by line:");
            Files.lines(path).forEach(System.out::println);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Example 2: File Operations**
```java
import java.nio.file.*;
import java.io.IOException;

public class FileOperationsDemo {
    public static void main(String[] args) {
        Path source = Paths.get("source.txt");
        Path destination = Paths.get("destination.txt");
        
        try {
            // Create file
            if (!Files.exists(source)) {
                Files.createFile(source);
                System.out.println("File created");
            }
            
            // Write content
            Files.write(source, "Hello, NIO!".getBytes());
            
            // Copy file
            Files.copy(source, destination, StandardCopyOption.REPLACE_EXISTING);
            System.out.println("File copied");
            
            // Move/Rename file
            Path newPath = Paths.get("renamed.txt");
            Files.move(destination, newPath, StandardCopyOption.REPLACE_EXISTING);
            System.out.println("File moved/renamed");
            
            // Delete file
            Files.delete(newPath);
            System.out.println("File deleted");
            
            // Check file properties
            System.out.println("Exists: " + Files.exists(source));
            System.out.println("Readable: " + Files.isReadable(source));
            System.out.println("Writable: " + Files.isWritable(source));
            System.out.println("Size: " + Files.size(source) + " bytes");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Example 3: Directory Operations**
```java
import java.nio.file.*;
import java.io.IOException;
import java.util.stream.Stream;

public class DirectoryOperationsDemo {
    public static void main(String[] args) {
        Path dir = Paths.get("test_directory");
        
        try {
            // Create directory
            if (!Files.exists(dir)) {
                Files.createDirectory(dir);
                System.out.println("Directory created");
            }
            
            // Create multiple directories
            Path multiDir = Paths.get("parent/child/grandchild");
            Files.createDirectories(multiDir);
            System.out.println("Multiple directories created");
            
            // List directory contents
            System.out.println("\nDirectory contents:");
            try (Stream<Path> paths = Files.list(Paths.get("."))) {
                paths.forEach(System.out::println);
            }
            
            // Walk directory tree
            System.out.println("\nDirectory tree:");
            try (Stream<Path> paths = Files.walk(Paths.get("."))) {
                paths.filter(Files::isRegularFile)
                     .forEach(System.out::println);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 🔥 Best Practices

### 1. Always Use Try-with-Resources

**❌ Bad:**
```java
FileReader reader = null;
try {
    reader = new FileReader("file.txt");
    // Read file
} catch (IOException e) {
    e.printStackTrace();
} finally {
    try {
        if (reader != null) {
            reader.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

**✅ Good:**
```java
try (FileReader reader = new FileReader("file.txt")) {
    // Read file
    // Automatically closed
} catch (IOException e) {
    e.printStackTrace();
}
```

### 2. Use Buffered Streams for Large Files

**❌ Bad:**
```java
try (FileReader reader = new FileReader("large_file.txt")) {
    int ch;
    while ((ch = reader.read()) != -1) {
        // Slow - one character at a time
    }
}
```

**✅ Good:**
```java
try (BufferedReader reader = new BufferedReader(new FileReader("large_file.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        // Fast - reads in chunks
    }
}
```

### 3. Use NIO for Better Performance

**❌ Traditional I/O:**
```java
List<String> lines = new ArrayList<>();
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        lines.add(line);
    }
}
```

**✅ NIO (simpler and faster):**
```java
List<String> lines = Files.readAllLines(Paths.get("file.txt"));
```

### 4. Handle Exceptions Properly

```java
import java.io.*;
import java.nio.file.*;

public class ExceptionHandlingDemo {
    public static void main(String[] args) {
        Path path = Paths.get("data.txt");
        
        try {
            List<String> lines = Files.readAllLines(path);
            System.out.println(lines);
            
        } catch (NoSuchFileException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (AccessDeniedException e) {
            System.err.println("Access denied: " + e.getMessage());
        } catch (IOException e) {
            System.err.println("I/O error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### 5. Always Define serialVersionUID

**✅ Good:**
```java
public class User implements Serializable {
    private static final long serialVersionUID = 1L;  // Define explicitly
    
    private String username;
    private String email;
}
```

### 6. Use Transient for Sensitive Data

**✅ Good:**
```java
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
    
    private String username;
    private String email;
    private transient String password;  // Don't serialize password
    private transient String creditCard;  // Don't serialize sensitive data
}
```

---

## 🔥 Key Interview Questions

**Q1: What is the difference between Byte Streams and Character Streams?**

**Answer:**
- **Byte Streams**: Handle binary data (8-bit), use InputStream/OutputStream, used for images/videos/audio
- **Character Streams**: Handle text data (16-bit Unicode), use Reader/Writer, used for text files, handles character encoding automatically

**Q2: Why use BufferedReader instead of FileReader?**

**Answer:**
- **Performance**: BufferedReader reads data in chunks (8192 chars buffer) instead of one character at a time
- **Convenience**: Provides `readLine()` method
- **Efficiency**: Reduces number of I/O operations significantly (10x faster)

**Q3: What is Serialization? Why is it used?**

**Answer:**
Serialization converts an object into a byte stream for:
- **Persistence**: Save object state to disk
- **Network Transfer**: Send objects over network (RMI, sockets)
- **Caching**: Store objects in cache
- **Deep Copy**: Create exact copy of objects

Class must implement `Serializable` interface.

**Q4: What is the purpose of serialVersionUID?**

**Answer:**
- Unique identifier for each Serializable class
- JVM verifies compatibility during deserialization
- Prevents `InvalidClassException` when class structure changes
- Should be explicitly defined to maintain version control

**Q5: What is the transient keyword?**

**Answer:**
- Prevents specific fields from being serialized
- Used for sensitive data (passwords, SSN) or derived data
- After deserialization, transient fields have default values (null, 0, false)

Example:
```java
class User implements Serializable {
    private String username;
    private transient String password;  // Not serialized
}
```

**Q6: Difference between FileWriter and BufferedWriter?**

| Feature | FileWriter | BufferedWriter |
|---------|------------|----------------|
| **Buffering** | No | Yes (8192 chars) |
| **Performance** | Slow | Fast |
| **newLine()** | No | Yes |
| **Use Case** | Small files | Large files |

**Q7: What happens if parent class doesn't implement Serializable but child does?**

**Answer:**
- Child can be serialized, but parent fields are NOT serialized
- During deserialization, parent's no-arg constructor is called
- Child constructor is NOT called during deserialization
- Parent must have a no-arg constructor

**Q8: Can we serialize static fields?**

**Answer:**
No. Static fields belong to the class, not the object. Serialization saves object state, not class state.

**Q9: What is the difference between java.io and java.nio?**

**Answer:**
- **java.io**: Stream-oriented, blocking I/O, traditional approach
- **java.nio**: Buffer-oriented, non-blocking I/O, better performance, introduced in Java 1.4

**Q10: How to read a file line by line efficiently?**

**Answer:**
```java
// Method 1: BufferedReader (Traditional)
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}

// Method 2: NIO (Java 7+)
Files.lines(Paths.get("file.txt")).forEach(System.out::println);
```

**Q11: What is try-with-resources?**

**Answer:**
- Introduced in Java 7
- Automatically closes resources (files, streams, connections)
- Resources must implement `AutoCloseable` interface
- Eliminates need for finally block to close resources

Example:
```java
try (FileReader reader = new FileReader("file.txt")) {
    // Use reader
} // Automatically closed
```

**Q12: How to copy a file in Java?**

**Answer:**
```java
// Method 1: NIO (Simplest)
Files.copy(Paths.get("source.txt"), Paths.get("dest.txt"), 
           StandardCopyOption.REPLACE_EXISTING);

// Method 2: Byte Streams
try (FileInputStream in = new FileInputStream("source.txt");
     FileOutputStream out = new FileOutputStream("dest.txt")) {
    byte[] buffer = new byte[1024];
    int bytesRead;
    while ((bytesRead = in.read(buffer)) != -1) {
        out.write(buffer, 0, bytesRead);
    }
}
```

---

## ✅ Key Takeaways

### Part 6 Summary:

**1. File Handling Basics:**
- ✅ Java I/O uses streams for reading/writing data
- ✅ File class manages file metadata, not content
- ✅ Always use try-with-resources to auto-close resources

**2. Streams:**
- ✅ **Byte Streams**: Binary data (InputStream/OutputStream)
- ✅ **Character Streams**: Text data (Reader/Writer)
- ✅ **Buffered Streams**: Better performance for large files

**3. File Operations:**
- ✅ Use File class for file/directory operations
- ✅ Use FileReader/FileWriter for text files
- ✅ Use BufferedReader/BufferedWriter for efficiency
- ✅ Use NIO (Files, Paths) for modern, simpler code

**4. Serialization:**
- ✅ Converts objects to byte streams
- ✅ Requires implementing Serializable interface
- ✅ Use serialVersionUID for version control
- ✅ Use transient for sensitive/derived data
- ✅ Static fields are NOT serialized

**5. Best Practices:**
- ✅ Use try-with-resources for automatic resource management
- ✅ Use BufferedReader/Writer for large files
- ✅ Use NIO for better performance
- ✅ Handle exceptions properly
- ✅ Always define serialVersionUID in Serializable classes

---

## 📝 Practice Questions

1. Write a program to copy content from one file to another using Byte Streams
2. Create a program to count the number of words, lines, and characters in a text file
3. Implement a program to read a CSV file and parse its content
4. Write a program to serialize and deserialize an ArrayList of objects
5. Create a program to append content to an existing file without overwriting
6. Implement a program to list all files and directories recursively
7. Write a program to find and replace text in a file
8. Create a program to compare two files and check if they're identical
9. Implement a program to split a large file into multiple smaller files
10. Write a program to merge multiple text files into one

---

## 📄 Cheatsheet

### 🔥 Quick Reference Guide - File Handling & I/O

#### 1️⃣ Streams Hierarchy

```
java.io Package
├── Byte Streams (8-bit)          └── Character Streams (16-bit)
    ├── InputStream                   ├── Reader
    │   ├── FileInputStream           │   ├── FileReader
    │   ├── BufferedInputStream       │   ├── BufferedReader
    │   └── ObjectInputStream         │   └── InputStreamReader
    └── OutputStream                  └── Writer
        ├── FileOutputStream              ├── FileWriter
        ├── BufferedOutputStream          ├── BufferedWriter
        └── ObjectOutputStream            └── OutputStreamWriter
```

---

#### 2️⃣ Byte Streams vs Character Streams

| Feature | Byte Streams | Character Streams |
|---------|--------------|-------------------|
| **Data Type** | Binary (8-bit) | Text (16-bit Unicode) |
| **Classes** | InputStream/OutputStream | Reader/Writer |
| **Use For** | Images, videos, audio | Text files, logs |
| **Encoding** | ❌ No | ✅ Yes (UTF-8, etc.) |

**Quick Rule:** Binary files → Byte Streams | Text files → Character Streams

---

#### 3️⃣ File Class - Essential Methods

```java
// Creation & Deletion
file.createNewFile()        // Create file
dir.mkdir()                 // Create single directory
dir.mkdirs()                // Create multiple directories
file.delete()               // Delete file/empty dir

// Information
file.exists()               // Check existence
file.getName()              // Get name
file.getAbsolutePath()      // Get full path
file.length()               // Get size in bytes
file.isFile()               // Is it a file?
file.isDirectory()          // Is it a directory?
file.canRead()              // Can read?
file.canWrite()             // Can write?

// Directory Listing
dir.list()                  // Get String[] of names
dir.listFiles()             // Get File[] objects
```

**💡 Remember:** File class manages metadata only, NOT content!

---

#### 4️⃣ FileReader vs BufferedReader

| Aspect | FileReader | BufferedReader |
|--------|------------|----------------|
| **Speed** | 🐌 Slow | ⚡ Fast (10-100x) |
| **Buffer** | ❌ None | ✅ 8192 chars |
| **readLine()** | ❌ No | ✅ Yes |
| **System Calls** | Per character | Per buffer |
| **Use For** | Small files | Large files |

**Best Practice:**
```java
// ❌ Never use FileReader alone for large files
try (FileReader fr = new FileReader("file.txt")) { }

// ✅ Always wrap with BufferedReader
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) { }
```

---

#### 5️⃣ Reading Files - Quick Patterns

```java
// Pattern 1: BufferedReader (Line by line)
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
}

// Pattern 2: NIO (Java 7+) - Simplest
List<String> lines = Files.readAllLines(Paths.get("file.txt"));

// Pattern 3: NIO Stream (Java 8+)
Files.lines(Paths.get("file.txt")).forEach(System.out::println);
```

---

#### 6️⃣ Writing Files - Quick Patterns

```java
// Pattern 1: BufferedWriter
try (BufferedWriter bw = new BufferedWriter(new FileWriter("file.txt"))) {
    bw.write("Hello World");
    bw.newLine();  // Platform-independent newline
}

// Pattern 2: FileWriter (Append mode)
try (FileWriter fw = new FileWriter("file.txt", true)) {  // true = append
    fw.write("Appended text");
}

// Pattern 3: NIO (Java 7+)
Files.write(Paths.get("file.txt"), "Hello World".getBytes());
```

---

#### 7️⃣ Serialization - Key Points

**Requirements:**
```java
class Student implements Serializable {  // 1. Implement Serializable
    private static final long serialVersionUID = 1L;  // 2. Define UID
    
    private String name;              // ✅ Serialized
    private transient String password;  // ❌ NOT serialized
    private static String school;      // ❌ NOT serialized (static)
}
```

**Serialization:**
```java
try (ObjectOutputStream out = new ObjectOutputStream(
        new FileOutputStream("object.ser"))) {
    out.writeObject(student);
}
```

**Deserialization:**
```java
try (ObjectInputStream in = new ObjectInputStream(
        new FileInputStream("object.ser"))) {
    Student student = (Student) in.readObject();
}
```

**Remember:**
- ✅ Class must implement `Serializable`
- ✅ Always define `serialVersionUID`
- ✅ Use `transient` for sensitive data (passwords, SSN)
- ❌ Static fields NOT serialized
- ❌ Transient fields become null/0/false after deserialization

---

#### 8️⃣ Inheritance & Serialization Rules

| Scenario | Parent Serializable | Child Serializable | Result |
|----------|---------------------|--------------------|---------|
| **Case 1** | ✅ Yes | Inherits | Both serialized |
| **Case 2** | ❌ No | ✅ Yes | Only child serialized |
| **Case 3** | ✅ Yes | ❌ No | Both serialized |

**Case 2 Important:** 
- Parent fields NOT serialized
- Parent constructor called during deserialization
- Child constructor NOT called during deserialization

---

#### 9️⃣ NIO (New I/O) - Quick Reference

```java
// File Operations
Path path = Paths.get("file.txt");
Files.exists(path)                    // Check existence
Files.createFile(path)                // Create file
Files.delete(path)                    // Delete file
Files.copy(source, dest)              // Copy file
Files.move(source, dest)              // Move/rename

// Reading
List<String> lines = Files.readAllLines(path);
byte[] bytes = Files.readAllBytes(path);
Files.lines(path).forEach(System.out::println);

// Writing
Files.write(path, "content".getBytes());
Files.write(path, lines);

// Directory Operations
Files.createDirectory(path)           // Single directory
Files.createDirectories(path)         // Multiple directories
Files.list(path).forEach(System.out::println);  // List contents
Files.walk(path).forEach(System.out::println);  // Recursive list
```

---

#### 🔟 Try-with-Resources (Must Know!)

**❌ Old Way (Java 6):**
```java
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("file.txt"));
    // Use br
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (br != null) br.close();  // Manual close
}
```

**✅ Modern Way (Java 7+):**
```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    // Use br
}  // Automatically closed!
```

**Multiple Resources:**
```java
try (FileInputStream in = new FileInputStream("input.txt");
     FileOutputStream out = new FileOutputStream("output.txt")) {
    // Use both
}  // Both automatically closed!
```

---

#### 1️⃣1️⃣ Common File Operations - One-Liners

```java
// Copy file (NIO)
Files.copy(Paths.get("source.txt"), Paths.get("dest.txt"), 
           StandardCopyOption.REPLACE_EXISTING);

// Read entire file
String content = new String(Files.readAllBytes(Paths.get("file.txt")));

// Write to file
Files.write(Paths.get("file.txt"), "Hello".getBytes());

// Count lines
long lineCount = Files.lines(Paths.get("file.txt")).count();

// Check if file exists
boolean exists = new File("file.txt").exists();
boolean exists2 = Files.exists(Paths.get("file.txt"));
```

---

#### 1️⃣2️⃣ Performance Tips 🚀

1. **Always use BufferedReader/BufferedWriter** for text files
   - 10-100x faster than FileReader/FileWriter
   
2. **Use NIO for simple operations**
   - `Files.readAllLines()` is simpler and faster
   
3. **Try-with-resources for automatic cleanup**
   - Prevents resource leaks
   
4. **Byte streams for binary files**
   - Faster for images, videos, audio
   
5. **Character streams for text files**
   - Handles encoding automatically

---

#### 1️⃣3️⃣ Common Exceptions

```java
try {
    // File operations
} catch (FileNotFoundException e) {
    // File doesn't exist
} catch (NoSuchFileException e) {  // NIO version
    // File doesn't exist
} catch (AccessDeniedException e) {
    // No permission
} catch (IOException e) {
    // General I/O error
} catch (ClassNotFoundException e) {  // Deserialization only
    // Class not found during deserialization
}
```

---

#### 1️⃣4️⃣ Interview Quick Answers

**Q: FileReader vs BufferedReader?**
> BufferedReader is 10-100x faster, has readLine(), uses 8192 char buffer

**Q: Byte vs Character streams?**
> Byte for binary (images), Character for text (handles encoding)

**Q: What is serialization?**
> Converting object to byte stream for storage/network transfer

**Q: What is transient?**
> Keyword to prevent field from being serialized (for sensitive data)

**Q: What is serialVersionUID?**
> Unique ID for version control during serialization/deserialization

**Q: Are static fields serialized?**
> No, static belongs to class, not object

**Q: java.io vs java.nio?**
> java.io: Stream-oriented, blocking | java.nio: Buffer-oriented, non-blocking, faster

**Q: What is try-with-resources?**
> Auto-closes resources (Java 7+), implements AutoCloseable interface

---

#### 1️⃣5️⃣ Golden Rules 🏆

1. ✅ **Always use try-with-resources** for automatic resource management
2. ✅ **Always wrap FileReader with BufferedReader** for large files
3. ✅ **Use NIO for simple operations** - cleaner and faster
4. ✅ **Define serialVersionUID** in every Serializable class
5. ✅ **Use transient** for passwords, SSN, credit cards
6. ✅ **Character streams for text**, byte streams for binary
7. ✅ **Check file.exists()** before operations
8. ✅ **Handle exceptions properly** - don't swallow them
9. ✅ **Close resources** - even if not using try-with-resources
10. ✅ **Use buffered classes** for better performance

---

#### 1️⃣6️⃣ Code Templates (Copy-Paste Ready)

**Template 1: Read Text File**
```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine()) != null) {
        // Process line
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

**Template 2: Write Text File**
```java
try (BufferedWriter bw = new BufferedWriter(new FileWriter("file.txt"))) {
    bw.write("Hello World");
    bw.newLine();
} catch (IOException e) {
    e.printStackTrace();
}
```

**Template 3: Serialize Object**
```java
try (ObjectOutputStream out = new ObjectOutputStream(
        new FileOutputStream("object.ser"))) {
    out.writeObject(object);
} catch (IOException e) {
    e.printStackTrace();
}
```

**Template 4: Deserialize Object**
```java
try (ObjectInputStream in = new ObjectInputStream(
        new FileInputStream("object.ser"))) {
    MyClass obj = (MyClass) in.readObject();
} catch (IOException | ClassNotFoundException e) {
    e.printStackTrace();
}
```

**Template 5: Copy File**
```java
// Method 1: NIO (Simplest)
Files.copy(Paths.get("source.txt"), Paths.get("dest.txt"), 
           StandardCopyOption.REPLACE_EXISTING);

// Method 2: Byte Streams (Manual)
try (FileInputStream in = new FileInputStream("source.txt");
     FileOutputStream out = new FileOutputStream("dest.txt")) {
    byte[] buffer = new byte[1024];
    int bytesRead;
    while ((bytesRead = in.read(buffer)) != -1) {
        out.write(buffer, 0, bytesRead);
    }
}
```

---

### 📌 Memory Joggers

**Buffered = Better** (Always use buffered classes for performance)

**TRY = Take Resources, Yay!** (Try-with-resources auto-closes)

**TRANSIENT = Temporary Removal ANSwering Securely In ENcryption Time** (Don't serialize)

**SERIALIZABLE = Save Everything Reversibly In A Logical Infinite Zone Accessible By Loading Every Element** (Object → Byte Stream)

**NIO = New, Improved, Optimal** (Better than old I/O)

---

**🎯 Master These & You're Set for Interviews!**

---

**Next Topics:** Part 7 (JVM & Memory Management), Part 8 (Other Important Concepts)
