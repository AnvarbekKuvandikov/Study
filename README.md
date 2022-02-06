# 1. What is Reactive Programming

Reactive Programming is basically event-based asynchronous programming. Everything you see is an asynchronous data stream, which can be observed and an action will be taken place when it emits values. You can create data stream out of anything; variable changes, click events, http calls, data storage, errors and what not. When it says asynchronous, that means every code module runs on its own thread thus executing multiple code blocks simultaneously.

An advantage of asynchronous approach is, as every task runs on its own thread, all the task can start simultaneously and amount of time takes complete all the tasks is equivalent to the longer task in the list. When it comes to mobile apps, as the tasks runs on background thread, you can achieve seamless user experience without blocking main thread.

- A simple example (from Wikipedia) can be x = y + z; where the sum of y and z is assigned to x. In reactive programming, when the y value changes, the value of x will be updated automatically without re-executing the x = y + z statement. This can be achieved by observing the value of y or z.

- An array list can be a data steam and an action can be taken when each item it emitted. May be you want to filter out the even numbers and ignoring the odd numbers. This can be done using usual loops and conditional statements, but in reactive programming you can achieve this in a completely different approach.


When you start your app in Reactive Programming, the way you design your architecture and the way you write code changes completely. It’s even becomes more powerful when met with Clean Architecture, MVP, MVVM and other design patters.


# 2. Reactive Extensions


Reactive Extensions (**ReactiveX** or **RX**) is a library that follows Reactive Programming principles i.e compose asynchronous and event based programs by using observable sequence. These libraries provides set of interfaces and methods which helps developers write clean and simpler code.

Reactive Extensions are available in multiple languages **C++ (RxCpp), C# (Rx.NET), Java (RxJava), Kotlin (RxKotlin), Swift (RxSwift)** and lot more. We specifically interested in **RxJava** and **RxAndroid** as android is our focused area.


# 3. What is RxJava

**RxJava** is Java implementation of Reactive Extension **(from Netflix)**. Basically it’s a library that composes asynchronous events by following Observer Pattern. You can create asynchronous data stream on any thread, transform the data and consumed it by an Observer on any thread. The library offers wide range of amazing operators like map, combine, merge, filter and lot more that can be applied onto data stream.

# 4. What is RxAndroid

RxAndroid is specific to Android Platform with few added classes on top of RxJava. More specifically, Schedulers are introduced in RxAndroid (AndroidSchedulers.mainThread()) which plays major role in supporting **multithreading** concept in android applications. Schedulers basically decides the thread on which a particular code runs whether on background thread or main thread. Apart from it everything we use is from RxJava library only.


Even through there are lot of Schedulers available, **Schedulers.io()** and **AndroidSchedulers.mainThread()** are extensively used in android programming. Below are the list of schedulers available and their brief introduction.

- **Schedulers.io()** – This is used to perform non CPU-intensive operations like making network calls, reading disc / files, database operations etc., This maintains pool of threads.

- **AndroidSchedulers.mainThread()** – This provides access to android Main Thread / UI Thread. Usually operations like updating UI, user interactions happens on this thread. We shouldn’t perform any intensive operations on this thread as it makes the app glitchy or ANR dialog can be thrown.

- **Schedulers.newThread()** – Using this, a new thread will be created each time a task is scheduled. It’s usually suggested not to use schedular unless there is a very long running operation. The threads created via newThread() won’t be reused.

- **Schedulers.computation()** – This schedular can be used to perform CPU-intensive operations like processing huge data, bitmap processing etc., The number of threads created using this scheduler completely depends on number CPU cores available.

- **Schedulers.single()** – This scheduler will execute all the tasks in sequential order they are added. This can be used when there is necessity of sequential execution is required.

- **Schedulers.immediate()** – This scheduler executes the the task immediately in synchronous way by blocking the main thread.

- **Schedulers.trampoline()** – It executes the tasks in First In – First Out manner. All the scheduled tasks will be executed one by one by limiting the number of background threads to one. 

- **Schedulers.from()** – This allows us to create a scheduler from an executor by limiting number of threads to be created. When thread pool is occupied, tasks will be queued.

Now we have the basic concepts needed. Let’s start with some key concepts of RxJava that everyone should aware of.

# 5. RxJava Basics: Observable, Observer

RxJava is all about two key components: **Observable** and **Observer**. In addition to these, there are other things like **Schedulers**, **Operators** and **Subscription**.

- **Observable**: Observable is a data stream that do some work and emits data.

- **Observer**: Observer is the counter part of Observable. It receives the data emitted by Observable.

- **Subscription**: The bonding between Observable and Observer is called as Subscription. There can be multiple Observers subscribed to a single Observable.

- **Operator / Transformation**: Operators modifies the data emitted by Observable before an observer receives them.

- **Schedulers**: Schedulers decides the thread on which Observable should emit the data and on which Observer should receives the data i.e background thread, main thread etc.,

# 6. RxJava Basic Examples

Now we have good theoretical knowledge about Reactive Programming, RxJava and RxAndroid. Let’s jump on to some code examples to understand the concepts better.

### Adding Dependencies

To get started, you need to add the RxJava and RxAndroid dependencies to your projects build.gradle and sync the project.

```
// RxJava
implementation 'io.reactivex.rxjava2:rxjava:2.1.9'
 
// RxAndroid
implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
```

# The Basic Steps

**1.** Create an **Observable** that emits data. Below we have created an Observable that emits list of animal names. Here **just()** operator is used to emit few animal names.

```
Observable<String> animalsObservable = Observable.just("Ant", "Bee", "Cat", "Dog", "Fox");
```

**2.** Create an Observer that listen to Observable. Observer provides the below interface methods to know the the state of Observable.

- **onSubscribe()**: Method will be called when an Observer subscribes to Observable.
- **onNext()**: This method will be called when Observable starts emitting the data.
- **onError()**: In case of any error, onError() method will be called.
- **onComplete()**: When an Observable completes the emission of all the items, onComplete() will be called.

```
Observer<String> animalsObserver = getAnimalsObserver();
 
private Observer<String> getAnimalsObserver() {
        return new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "onSubscribe");
            }
 
            @Override
            public void onNext(String s) {
                Log.d(TAG, "Name: " + s);
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All items are emitted!");
            }
        };
    }
```

**3.** Make **Observer subscribe to Observable** so that it can start receiving the data. Here, you can notice two more methods, **observeOn()** and **subscribeOn()**.

- **subscribeOn(Schedulers.io())**: This tell the Observable to run the task on a background thread.
- **observeOn(AndroidSchedulers.mainThread())**: This tells the Observer to receive the data on android UI thread so that you can take any UI related actions.

```
animalsObservable
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(animalsObserver);
```

If you run the program, you can see the below output in your LogCat.

```
onSubscribe
Name: Ant
Name: Bee
Name: Cat
Name: Dog
Name: Fox
All items are emitted!
```

That’s all, you just wrote your first RxJava program. We are going to learn more about Schedulers and Observers in subsequent articles. But for now this information is sufficient to get started.

## Example 1: Basic Observable, Observer
Here is complete code of the above example. Run the activity and check the output in LogCat.

```
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
 
 
import info.androidhive.rxandroidexamples.R;
import io.reactivex.Observable;
import io.reactivex.Observer;
import io.reactivex.android.schedulers.AndroidSchedulers;
import io.reactivex.disposables.Disposable;
import io.reactivex.schedulers.Schedulers;
 
public class Example1Activity extends AppCompatActivity {
 
    /**
     * Basic Observable, Observer, Subscriber example
     * Observable emits list of animal names
     */
 
    private static final String TAG = Example1Activity.class.getSimpleName();
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example1);
 
        // observable
        Observable<String> animalsObservable = getAnimalsObservable();
 
        // observer
        Observer<String> animalsObserver = getAnimalsObserver();
 
        // observer subscribing to observable
        animalsObservable
                .observeOn(Schedulers.io())
                .subscribeOn(AndroidSchedulers.mainThread())
                .subscribe(animalsObserver);
    }
 
    private Observer<String> getAnimalsObserver() {
        return new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "onSubscribe");
            }
 
            @Override
            public void onNext(String s) {
                Log.d(TAG, "Name: " + s);
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All items are emitted!");
            }
        };
    }
 
    private Observable<String> getAnimalsObservable() {
        return Observable.just("Ant", "Bee", "Cat", "Dog", "Fox");
    }
}
```

**Output:**
```
onSubscribe
Name: Ant
Name: Bee
Name: Cat
Name: Dog
Name: Fox
All items are emitted!
```


## Example 2: Introducing Disposable
In this example we gonna introduce new component called Disposable.

- **Disposable**: Disposable is used to dispose the subscription when an Observer no longer wants to listen to Observable. In android disposable are very useful in avoiding memory leaks. Let’s say you are making a long running network call and updating the UI. By the time network call completes its work, if the activity / fragment is already destroyed, as the Observer subscription is still alive, it tries to update already destroyed activity. In this case it can throw a memory leak. So using the Disposables, the un-subscription can be when the activity is destroyed.


In the below example you can see a **Disposable** is used and calling **disposable.dispose()** in **onDestroy()** will **un-subscribe** the Observer.

```
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
 
import info.androidhive.rxandroidexamples.R;
import io.reactivex.Observable;
import io.reactivex.Observer;
import io.reactivex.android.schedulers.AndroidSchedulers;
import io.reactivex.disposables.CompositeDisposable;
import io.reactivex.disposables.Disposable;
import io.reactivex.observers.DisposableObserver;
import io.reactivex.schedulers.Schedulers;
 
public class Example2Activity extends AppCompatActivity {
 
    /**
     * Basic Observable, Observer, Subscriber example
     * Observable emits list of animal names
     * You can see Disposable introduced in this example
     */
    private static final String TAG = Example2Activity.class.getSimpleName();
 
    private Disposable disposable;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example2);
 
        // observable
        Observable<String> animalsObservable = getAnimalsObservable();
 
        // observer
        Observer<String> animalsObserver = getAnimalsObserver();
 
        // observer subscribing to observable
        animalsObservable
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribeWith(animalsObserver);
    }
 
    private Observer<String> getAnimalsObserver() {
        return new Observer<String>() {
 
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "onSubscribe");
                disposable = d;
            }
 
            @Override
            public void onNext(String s) {
                Log.d(TAG, "Name: " + s);
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All items are emitted!");
            }
        };
    }
 
    private Observable<String> getAnimalsObservable() {
        return Observable.just("Ant", "Bee", "Cat", "Dog", "Fox");
    }
 
    @Override
    protected void onDestroy() {
        super.onDestroy();
 
        // don't send events once the activity is destroyed
        disposable.dispose();
    }
}
```

This will generate the same output as earlier example.

## Example 3: Introducing Operator

Now we’ll see another example by introducing an operator to transform the emitted data. In the below example **filter()** operator is used to filter out the emitted data.

- **filter()** operator filters the data by applying a conditional statement. The data which meets the condition will be emitted and the remaining will be ignored.


In the below example the animal names which starts with **letter`b`** will be filtered.

```
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
 
import info.androidhive.rxandroidexamples.R;
import io.reactivex.Observable;
import io.reactivex.Observer;
import io.reactivex.android.schedulers.AndroidSchedulers;
import io.reactivex.disposables.Disposable;
import io.reactivex.functions.Predicate;
import io.reactivex.schedulers.Schedulers;
 
public class Example3Activity extends AppCompatActivity {
 
    /**
     * Basic Observable, Observer, Subscriber example
     * Observable emits list of animal names
     * You can see Disposable introduced in this example
     */
    private static final String TAG = Example3Activity.class.getSimpleName();
 
    private Disposable disposable;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example3);
 
        // observable
        Observable<String> animalsObservable = getAnimalsObservable();
 
        // observer
        Observer<String> animalsObserver = getAnimalsObserver();
 
        // observer subscribing to observable
        animalsObservable
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .filter(new Predicate<String>() {
                    @Override
                    public boolean test(String s) throws Exception {
                        return s.toLowerCase().startsWith("b");
                    }
                })
                .subscribeWith(animalsObserver);
    }
 
    private Observer<String> getAnimalsObserver() {
        return new Observer<String>() {
 
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "onSubscribe");
                disposable = d;
            }
 
            @Override
            public void onNext(String s) {
                Log.d(TAG, "Name: " + s);
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All items are emitted!");
            }
        };
    }
 
    private Observable<String> getAnimalsObservable() {
        return Observable.fromArray(
                "Ant", "Ape",
                "Bat", "Bee", "Bear", "Butterfly",
                "Cat", "Crab", "Cod",
                "Dog", "Dove",
                "Fox", "Frog");
    }
 
    @Override
    protected void onDestroy() {
        super.onDestroy();
 
        // don't send events once the activity is destroyed
        disposable.dispose();
    }
}
```

If you run the example, you can see the animal names that starts with letter `b` printed in LogCat.

**Output:**

```
onSubscribe
Name: Bat
Name: Bee
Name: Bear
Name: Butterfly
All items are emitted!
```


## Example 4: Multiple Observers and CompositeDisposable

Consider a case where you have multiple Observables and Observers. Disposing them in Destroy one bye one is a tedious task and it can be error prone as you might forgot to dispose. In this case we can use **CompositeDisposable**.


- **CompositeDisposable**: Can maintain list of subscriptions in a pool and can dispose them all at once.
Usually we call **compositeDisposable.clear()** in **onDestroy()** method, but you can call anywhere in the code.

In the below example, you can notice two observers animalsObserver and **animalsObserverAllCaps** subscribed to same **Observable**. The both observers receives the same data but the data changes as different operators are applied on the stream.

- **animalsObserver**: – The **filter()** operator is used to filter the animal names starting with **letter`b`**.
- **animalsObserverAllCaps**: – The **filter()** operator is used to filter the animal names starting with **letter`c`**. Later **map()** operator is used to convert each animal name to all capital letters. Using multiple operators on a single observer is called **chaining of operators**.


```
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
 
import info.androidhive.rxandroidexamples.R;
import io.reactivex.Observable;
import io.reactivex.android.schedulers.AndroidSchedulers;
import io.reactivex.disposables.CompositeDisposable;
import io.reactivex.functions.Function;
import io.reactivex.functions.Predicate;
import io.reactivex.observers.DisposableObserver;
import io.reactivex.schedulers.Schedulers;
 
public class Example4Activity extends AppCompatActivity {
 
    /**
     * Basic Observable, Observer, Subscriber example
     * Observable emits list of animal names
     * You can see filter() operator is used to filter out the
     * animal names that starts with letter `b`
     */
    private static final String TAG = Example4Activity.class.getSimpleName();
 
    private CompositeDisposable compositeDisposable = new CompositeDisposable();
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example4);
 
        Observable<String> animalsObservable = getAnimalsObservable();
 
        DisposableObserver<String> animalsObserver = getAnimalsObserver();
 
        DisposableObserver<String> animalsObserverAllCaps = getAnimalsAllCapsObserver();
 
        /**
         * filter() is used to filter out the animal names starting with `b`
         * */
        compositeDisposable.add(
                animalsObservable
                        .subscribeOn(Schedulers.io())
                        .observeOn(AndroidSchedulers.mainThread())
                        .filter(new Predicate<String>() {
                            @Override
                            public boolean test(String s) throws Exception {
                                return s.toLowerCase().startsWith("b");
                            }
                        })
                        .subscribeWith(animalsObserver));
 
        /**
         * filter() is used to filter out the animal names starting with 'c'
         * map() is used to transform all the characters to UPPER case
         * */
 
        compositeDisposable.add(
                animalsObservable
                        .subscribeOn(Schedulers.io())
                        .observeOn(AndroidSchedulers.mainThread())
                        .filter(new Predicate<String>() {
                            @Override
                            public boolean test(String s) throws Exception {
                                return s.toLowerCase().startsWith("c");
                            }
                        })
                        .map(new Function<String, String>() {
                            @Override
                            public String apply(String s) throws Exception {
                                return s.toUpperCase();
                            }
                        })
                        .subscribeWith(animalsObserverAllCaps));
    }
 
    private DisposableObserver<String> getAnimalsObserver() {
        return new DisposableObserver<String>() {
 
            @Override
            public void onNext(String s) {
                Log.d(TAG, "Name: " + s);
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All items are emitted!");
            }
        };
    }
 
    private DisposableObserver<String> getAnimalsAllCapsObserver() {
        return new DisposableObserver<String>() {
 
 
            @Override
            public void onNext(String s) {
                Log.d(TAG, "Name: " + s);
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All items are emitted!");
            }
        };
    }
 
    private Observable<String> getAnimalsObservable() {
        return Observable.fromArray(
                "Ant", "Ape",
                "Bat", "Bee", "Bear", "Butterfly",
                "Cat", "Crab", "Cod",
                "Dog", "Dove",
                "Fox", "Frog");
    }
 
    @Override
    protected void onDestroy() {
        super.onDestroy();
 
        // don't send events once the activity is destroyed
        compositeDisposable.clear();
    }
}
```

If you run this example, you can see the below output. Here the animal names that starts with `B` are observed by **animalsObserver**. All the names starting with letter `c` and in all capital letters are observed by **animalsObserverAllCaps**.


```
Name: Bat
Name: Bee
Name: Bear
Name: Butterfly
All items are emitted!
Name: CAT
Name: CRAB
Name: COD
All items are emitted!
```

**Example 5: Custom Data Type, Operators**

Finally we’ll conclude this article with an interesting example. In this, instead of using just primitive data types, we are going to use a custom data type i.e **Note** model. We use same Observable and Observer concept here except the streamed data is of Note data type.

- Here **map()** operator is used to convert all the notes to all uppercase format.

```
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
 
import java.util.ArrayList;
import java.util.List;
 
import info.androidhive.rxandroidexamples.R;
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.android.schedulers.AndroidSchedulers;
import io.reactivex.disposables.CompositeDisposable;
import io.reactivex.functions.Function;
import io.reactivex.observers.DisposableObserver;
import io.reactivex.schedulers.Schedulers;
 
public class Example4Activity extends AppCompatActivity {
 
 
    /**
     * Basic Observable, Observer, Subscriber example
     * Introduced CompositeDisposable and DisposableObserver
     * The observable emits custom data type (Note) instead of primitive data types
     * ----
     * .map() operator is used to turn the note into all uppercase letters
     * ----
     * You can also notice we got rid of the below declarations
     * Observable<Note> notesObservable = getNotesObservable();
     * DisposableObserver<Note> notesObserver = getNotesObserver();
     */
    private static final String TAG = Example4Activity.class.getSimpleName();
 
    private CompositeDisposable disposable = new CompositeDisposable();
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example4);
 
        // add to Composite observable
        // .map() operator is used to turn the note into all uppercase letters
        disposable.add(getNotesObservable()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .map(new Function<Note, Note>() {
                    @Override
                    public Note apply(Note note) throws Exception {
                        // Making the note to all uppercase
                        note.setNote(note.getNote().toUpperCase());
                        return note;
                    }
                })
                .subscribeWith(getNotesObserver()));
    }
 
    private DisposableObserver<Note> getNotesObserver() {
        return new DisposableObserver<Note>() {
 
            @Override
            public void onNext(Note note) {
                Log.d(TAG, "Note: " + note.getNote());
            }
 
            @Override
            public void onError(Throwable e) {
                Log.e(TAG, "onError: " + e.getMessage());
            }
 
            @Override
            public void onComplete() {
                Log.d(TAG, "All notes are emitted!");
            }
        };
    }
 
    private Observable<Note> getNotesObservable() {
        final List<Note> notes = prepareNotes();
 
        return Observable.create(new ObservableOnSubscribe<Note>() {
            @Override
            public void subscribe(ObservableEmitter<Note> emitter) throws Exception {
                for (Note note : notes) {
                    if (!emitter.isDisposed()) {
                        emitter.onNext(note);
                    }
                }
 
                if (!emitter.isDisposed()) {
                    emitter.onComplete();
                }
            }
        });
    }
 
    private List<Note> prepareNotes() {
        List<Note> notes = new ArrayList<>();
        notes.add(new Note(1, "buy tooth paste!"));
        notes.add(new Note(2, "call brother!"));
        notes.add(new Note(3, "watch narcos tonight!"));
        notes.add(new Note(4, "pay power bill!"));
 
        return notes;
    }
 
    class Note {
        int id;
        String note;
 
        public Note(int id, String note) {
            this.id = id;
            this.note = note;
        }
 
        public int getId() {
            return id;
        }
 
        public String getNote() {
            return note;
        }
 
        public void setId(int id) {
            this.id = id;
        }
 
        public void setNote(String note) {
            this.note = note;
        }
    }
 
    @Override
    protected void onDestroy() {
        super.onDestroy();
        disposable.clear();
    }
}
```


**Output:**

```
Id: 1, note: BUY TOOTH PASTE!
Id: 2, note: CALL BROTHER!
Id: 3, note: WATCH NARCOS TONIGHT!
Id: 4, note: PAY POWER BILL!
All notes are emitted!
```



[orginal source](https://www.androidhive.info/RxJava/android-getting-started-with-reactive-programming/#rxjava-examples)
