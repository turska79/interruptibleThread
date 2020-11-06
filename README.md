# interruptibleThread

Class is modified version introduced in the book C++ Concurrency in Action by Anthony Williams.

Main class has simple and short interface containing templated constructor containing instance and function of desired class running the thread.
		
template<typename F, typename T> interruptibleThread(F function, T instance)
void interrupt()

Example usage:
Creating a thread:
interruptMe = new thread::interruptibleThread(&ExampleClass::exampleFunction, this);

Interrupting running thread:
interruptMe->interrupt()

Running thread function needs to check for interruptions in desired locations in the code:
thread::utils::interruptionPoint();

It also contains various methods for thread syncronization:
template<typename Lockable> void interruptibleWait(std::condition_variable_any& cv, Lockable& lk)
template<typename T> void interruptibleWait(std::future<T>& uf, std::unique_lock<std::mutex>& lk)
template<typename Predicate> void interruptibleWait(std::condition_variable& cv, std::unique_lock<std::mutex>& lk, Predicate pred)
template<typename Lockable> inline void InterruptFlag::wait(std::condition_variable_any& cv, Lockable& lk)
  
Wait loops use condition_variables wait_for funtion for waiting one millisecond between checking if they should continue running
so they will get spurious wakeups.

To avoid spurious wakeup usage of version using Predicate is recommended.
Example with Predicate where thread waits until Predicate function returns true or interrupt is called.
std::condition_variable cv;
std::mutex mutex;
std::unique_lock<std::mutex> lock(mutex);

auto function = std::bind(&Foo::readyToContinue, this);
thread::utils::interruptibleWait<decltype(function)>(cv, lock, function);
