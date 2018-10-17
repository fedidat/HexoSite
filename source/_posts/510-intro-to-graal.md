---
title: Intro to GraalVM
date: 2018-10-09 07:52:44
tags:
---

[GraalVM](https://www.graalvm.org/) is a high-performance polyglot VM being developed by Oracle. Using one interpreter to run all sorts of languages is not new. After all, the JVM itself runs all sorts of other languages, like jython for Python or JRuby for Ruby. But GraalVM is an attempt to make the fastest VM for all languages, and then use that basis to make calls between languages very fast and convenient.

There are two major components to the Graal project: its high performance Java compiler called Graal, and a language [abstract syntax tree](https://en.wikipedia.org/wiki/Abstract_syntax_tree) interpreter called the Truffle framework, which allows the implementation of other languages on top on the Graal framework. Various other components like Sulong or SubstrateVM have since merged into the Graal project towards this polyglot effort.

Time will tell whether GraalVM will end up being the best option for all major languages - and for new ones too - and whether we are headed to a polyglot world. Microservices might even go out of fashion without the need to separate teams that are using different languages, although you will see that Graal can also help with these.

In any case, I think we should all know about this potentially very useful tool. For an excellent presentation, I instead strongly recommend this [Youtube talk](https://www.youtube.com/watch?v=tEaEAq0L9Pk), which teaches a lot more tricks than just GraalVM related stuff.

Now for my guide. I am late to the party, after a wave of article following the open-sourcing of GraalVM in last April. So I will try to give an exhaustive review of GraalVM's abilities after the dust settled.

## 5-minute setup

GraalVM offers two editions: a free-and-open-source community edition hosted on [Github](https://github.com/oracle/graal/releases) and a professional edition offered on Oracle's site. The difference is that the enterprise edition is not free for commercial purposes, without major differences between them other than support. Both versions are only available on Linux and Mac.

Download the latest releases from https://github.com/oracle/graal/releases. In its `bin` folder you will find, among others, drop-in replacements for many utils:

* `javac` of which Graal is the brand-new AOT compiler, 
* `java` for jars and classes, 
* `lli`, an LLVM bytecode interpreter,
* `js`, a REPL JavaScript interpreter which can also run JS files,
* `node` for Node.js, together with `npm`, fully working with npm modules.

There are also many other utilities, such as:

* Many tools for JVM bytecode performance such as the Graal VisualVM still named `jvisualvm`, since running polyglot bytecode is GraalVM's prime purpose.
* `gu`, the Graal updater, from which additional languages may be installed to get more drop-in replacements such as `graalpython`, `ruby` or `R`, e.g `gu install ruby`. Execute `gu list -c` to see the available components.
* `native-image`, another highlight feature of the GraalVM, which builds a compiled executable or a library from Java applications. The result is not truly native -it runs on SubstrateVM, a new light JVM-, but is self-contained and minimal, and it also allows you to run Java applications with a process name other than `java.exe` (as does the JPMS).

You may decide to replace your PATH utils with these ones like so `export PATH=/path/to/graalvm/bin:$PATH`.

As you can see, the Graal project encompasses far beyond a compiler and an interpreter, and its ambitions are big.

## Examples

### Java performance benchmark

Since GraalVM's `java` is a drop-in replacement, running a benchmark between GraalVM and OpenJDK is as simple as switching `java` alternatives. I used the handy GUI `galternatives` for this.

As a benchmark, I used the [java-simple-stream-benchmark](https://github.com/graalvm/graalvm-demos/tree/master/java-simple-stream-benchmark):

```java 
    @Benchmark
    public int testMethod() {
        return Arrays.stream(new int[]{1, 2, 3, 4, 5, 6, 7, 8, 9, 10})
        .map(x -> x + 1)
        .map(x -> x * 2)
        .map(x -> x + 2)
        .reduce(0, Integer::sum);
    }
```

I averaged 115.403 ns/op for GraalVM 1.0.0-rc6 with JVM CI 0.48, compared to 174.055 ns/op for OpenJDK 10.0.2. Probably an underestimation of GraalVM's performance, as this is far from the 18x speedup touted by Oracle's talks.

### npm and node, polyglot with Java and Python interop

Let's dive into a practical example of the powerful polyglot feature of GraalVM, with its strong interoperability between languages.

Since there are so many combinations between languages, this is still work in progress. For example, `graalpython` can still not import external modules, and Javascript has the strongest interop right now. However, all available languages can interop to some extent, which I think is amazing. [This page](https://www.graalvm.org/docs/reference-manual/polyglot/#how-to-build-polyglot-applications) neatly shows how get from each language to the others.

Now, I'll be running a Java thread race based on [this demo](http://www.java2s.com/Code/Java/Threads/ThreadRaceDemo.htm). Here is my thread:

```java
public class RacingThread extends Thread {
    private int tick = 1;
    private int num;
    public RacingThread(int num) {
        this.num = num;
    }
  
    public void run() {
        while (tick < 200000) {
            tick++;
            if ((tick % 50000) == 0)
                System.out.println("Thread #" + num + ", tick = " + tick);
        }
    }
  }
```

To make things interesting, I'll be running it from Javascript. What's more, I will also be using npm and node to run an express.js server. Inside my endpoint, I will be using Python to manipulate regular expressions with Javascript: that's two levels of interop! Sounds like a lot, but the code is straightforward:

```js
const express = require('express')
const app = express()

//Java 'Hello world' with a standard class
var JavaPrinter = Java.type('java.lang.System')
JavaPrinter.out.println("Hello from GraalVM Java!")

// Thread race with Java
var RacingThread = Java.type('RacingThread')
var runners = [], NUMRUNNERS = 2;
for (var i = 0; i < NUMRUNNERS; i++) {
    runners[i] = new RacingThread(i);
    runners[i].setPriority(2);
}
for (var i = 0; i < NUMRUNNERS; i++)
runners[i].start();

//express.js server
app.get('/', function (req, res) {
  var text = 'Hello from GraalVM node!<br> '
  
  //Use Python interop, and use Javascript's `RegExp` inside!
  text += Polyglot.eval('python',
    `
import polyglot

message = "Hello from graalpython!"
print(message)

re = polyglot.eval(string="RegExp()", language="js")
pattern = re.compile("(Hello) from (.*)") #Javascript!
md = pattern.exec(message)
md[2] #return the second match
    `);

  res.send(text)
})

app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
})
```

Let's run this:

```bash
javac RunningThread.java
npm install express
node --polyglot --jvm demo.js --jvm.cp=.
```

Output (with one request):

    Hello from GraalVM Java!
    Thread #1, tick = 50000
    Thread #0, tick = 50000
    Thread #1, tick = 100000
    Thread #0, tick = 100000
    Thread #1, tick = 150000
    Thread #1, tick = 200000
    Thread #0, tick = 150000
    Thread #0, tick = 200000
    Example app listening on port 3000!
    Hello from graalpython!

And in the browser:

    Hello from GraalVM node!
    graalpython!

Although the edges are still rough, and GraalVM is far from a finished product. I think the benefit is clear: you no longer have to choose between Java's precision, Javascript's flexibility, Python's practicality, R's processing tooling, and so on. You can now combine the best tools and their libraries for each job in one simple runtime, without sacrificing simplicity or performance.

### Inspct with Chrome DevTools and profile with GraalVM

By the way, let's activate debugging and take a look at the excellent tools available. We just need to add the `--inspect` flag as the first argument of the `node` command above:

    $ ../node --inspect --polyglot --jvm demo.js --jvm.cp=.
    Debugger listening on port 9229.
    To start debugging, open the following URL in Chrome:
        chrome-devtools://devtools/bundled/js_app.html?ws=127.0.0.1:9229/33a10788-11d7538b50bef
    Hello from GraalVM Java!
    [...]

By copy-pasting the link into Chrome, we get to use the powerful Chrome Dev Tools. This argument works for all the GraalVM interpreter tools (`java`, `ruby`, etc).

![Chrome profiler example](/images/510-intro-to-graal/chrome-dev-tools.png)

And for users of JVisualVM, the "Graal VisualVM" is included in the GraalVM release as a tool still named `jvisualvm`. It looks and performs just like the familiar JVisualVM.

Arguably, these may be the best versatile debugging and profiling tools available to developers today. And in addition, usual Java debuggers can be used. The `mx` tool provided with GraalVM creates Eclipse configurations and launchers, and its `-d` flag then opens a port for remote debugging.

### Native image

Another highlighted feature of GraalVM is native images. These images contain a minimal and self-contained runtime. Think of the Java 9 module system but polyglot. Through the use of Substrate VM, native images can provide us with the same functionality as a Java program in a low-footprint and fast-startup package, with the added advantage of the executable having its own process name.

Sadly, SubstrateVM is still [limited](https://github.com/oracle/graal/blob/master/substratevm/LIMITATIONS.md), in particular due to the need to link native libraries or [reimplement native interfaces](https://cornerwings.github.io/2018/07/graal-native-methods/). So, after failing to integrate examples of image processing due to ImageIO's low-level JNI calls not working, I will give the polyglot example featured on [GraalVM's site](https://www.graalvm.org/docs/getting-started/#native-images):

```java
import java.io.*;
import java.util.stream.*;
import org.graalvm.polyglot.*;

public class PrettyPrintJSON {
    public static void main(String[] args) throws java.io.IOException {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        String input = reader.lines().collect(Collectors.joining(System.lineSeparator()));
        System.out.println(prettyPrint(input));
    }

    private static String prettyPrint(String input) {
        try (Context context = Context.create("js")) {
            Value parse = context.eval("js", "JSON.parse");
            Value stringify = context.eval("js", "JSON.stringify");
            Value result = stringify.execute(parse.execute(input));
            return result.asString();
        }
    }
}
```

Once again, the immediate benefit of using other languages' strong points (in this case JavaScript's strong JSON manipulation) as native utilities is evident. Let's make that into a native image:

    $ javac PrettyPrintJSON.java
    $ native-image --language:js PrettyPrintJSON
    Build on Server(pid: 10170, port: 39277)
    [prettyprintjson:10170]    classlist:   1,331.42 ms
    [prettyprintjson:10170]        (cap):     724.33 ms
    [prettyprintjson:10170]        setup:   1,801.26 ms
    [prettyprintjson:10170]   (typeflow):  24,805.02 ms
    [prettyprintjson:10170]    (objects):  40,926.76 ms
    [prettyprintjson:10170]   (features):   4,361.71 ms
    [prettyprintjson:10170]     analysis:  71,531.24 ms
    6931 method(s) included for runtime compilation
    [prettyprintjson:10170]     universe:   2,219.84 ms
    [prettyprintjson:10170]      (parse):   5,804.11 ms
    [prettyprintjson:10170]     (inline):   8,179.27 ms
    [prettyprintjson:10170]    (compile):  27,649.78 ms
    [prettyprintjson:10170]      compile:  44,439.05 ms
    [prettyprintjson:10170]        image:   7,320.81 ms
    [prettyprintjson:10170]        write:   4,928.16 ms
    [prettyprintjson:10170]      [total]: 133,677.70 ms

While native images are usually lean, this one weighs 90MB. This is probably due to the JavaScript runtime, and it is still relatively low compared to the 400MB+ of the JVM. Here too there is room for improvement, with low-hanging fruit like [unused classes](https://github.com/oracle/graal/issues/287) on the backlog.

Although the program failed to produce newlines for me, it does validate and tidy up the JSON a bit. And the execution time was indeed remarkably lower than through the JVM, mostly due to startup times:

    $ time java PrettyPrintJSON < time.json > /dev/null
    real    0m0.604s
    user    0m1.688s
    sys     0m0.104s
    $ time ./prettyprintjson < time.json > /dev/null
    real    0m0.040s
    user    0m0.012s
    sys     0m0.027s

For further real-world reading about native images, check this [dockerized microservice example](http://royvanrijn.com/blog/2018/09/part-2-native-microservice-in-graalvm/) and [repo](https://github.com/royvanrijn/graalvm-native-microservice).

Now, another neat thing about native images is that we can use them as dynamic libraries. Let's add an entry point in our previous class:

```java
    @CEntryPoint(name = "prettyprintjson")
    public static CCharPointer prettyprint(IsolateThread thread, CCharPointer input) {
        return CTypeConversion.toCString(prettyPrint(CTypeConversion.toJavaString(input))).get();
    }
```

And let's make a library:

    $ javac PrettyPrintJSON.java
    $ native-image --no-server -cp . --language:js -H:Kind=SHARED_LIBRARY -H:Name=libprettyprintjson

We get a few .h files for GraalVM thread boilerplate code, another .h file with our library already including it, and finally our new library, `libprettyprintjson.so`:

    $ objdump -x libprettyprintjson.so | grep prettyprint
    0000000001ea1df0 g     F .text  0000000000000139              prettyprint

Here is a C program to make use of our library:

```c
#include <stdio.h>
#include <libprettyprintjson.h>

int main(int argc, char **argv) {
  graal_isolate_t *isolate = NULL;
  graal_isolatethread_t *thread = NULL;
  if (graal_create_isolate(NULL, &isolate) != 0 || (thread = graal_current_thread(isolate)) == NULL) {
    fprintf(stderr, "initialization error\n");
    return 1;
  }
  
  char* input   = argv[1];
  printf("%s\n", prettyprint(thread, input));
  return 0;
}
```

Compile and run:

    $ gcc -I. -L. -ldistance prettyprintdemo.c -o prettyprintdemo
    $ ./prettyprintdemo '{"Hello": ["polyglot", "World"]   }'
    {"Hello":["polyglot","World"]}

### LLVM bytecode

The original idea of LLVM sounds similar to GraalVM: "making a modular and reusable compiler and toolchain technologies". Frontends for languages like C, C++, Rust or Haskell produce LLVM bitcode ("Intermediate Representation" or IR) which goes through the same LLVM optimizer and is then compiled to various assembly backends. This is one of the strengths of LLVM against GCC's more agressive but "magical" optimization techniques. Here is a representation from [the AOSA book](http://www.aosabook.org/en/llvm.html):

![LLVM schema](/images/510-intro-to-graal/llvm-schema.png)

It turns out that GraalVM made an interpreter for LLVM bitcode, `lli` (formerly named project Sulong), thereby potentially replacing the backends for these languages. And there are some good reasons to do this: 

* GraalVM's interoperability allows us to easily call the LLVM bitcode from other languages like Javascript or Java, 
* there are potential speedups from GraalVM's mix of AOT and JIT optimizations, rather than assembly which are onyl compiled AOT,
* the regular suite of GraalVM's debugging utilities applies to its LLVM bitcode interpreter as well,through the regular `--inspect` flag,
* finally, the usual advantage of intepreters: programs run everywhere (well, everywhere GraalVM runs).

I'll skip the boring Hello Worlds in C++ or Rust, and compile a large program in C. Since Clang's `-emit-llvm` flag doesn't work when linking several files together, I'll be using one of the [large single compilation unit C programs here](https://people.csail.mit.edu/smcc/projects/single-file-programs/), namely the Ogg Vorbis encoder.

    $ clang -c -emit-llvm oggenc.c
    $ lli oggenc.bc jfk_1963_0626_berliner.wav
    Opening with wav module: WAV file reader
    Encoding "jfk_1963_0626_berliner.wav" to
            "jfk_1963_0626_berliner.ogg"
    at quality 3.00
    Done encoding file "jfk_1963_0626_berliner.ogg"
            File length:  8m 37.0s
            Elapsed time: 1m 04.0s
            Rate:         8.0836
            Average bitrate: 69.0 kb/s

And the output jfk_1963_0626_berliner.ogg plays perfectly well. It took 1m04s to encode this WAV to OGG on my laptop. Let's compare to a Clang-compiled version.

    $ clang oggenc.c -lm -o oggenc
    $ ./oggenc ./jfk_1963_0626_berliner.wav
    Opening with wav module: WAV file reader
    Encoding "./jfk_1963_0626_berliner.wav" to
            "./jfk_1963_0626_berliner.ogg"
    at quality 3.00
    Done encoding file "./jfk_1963_0626_berliner.ogg"
            File length:  8m 37.0s
            Elapsed time: 0m 11.0s
            Rate:         46.8643
            Average bitrate: 69.0 kb/s

11 seconds. Again, performance-wise, there is room for improvement.

More interesting is the old version of GCC also on that site. When compiled to LLVM bitcode and interpreted with GraalVM, it indeed produces assembly, although that version shows its age, being 32-bit.

## Conclusion

That's it, I hope those shortened examples gave a good review of GraalVM's wide range of abilities. I think it is very promising, although there is still much work to do for it to win in all of these areas.