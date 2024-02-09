---
layout: post
title: Exploring Policy-Based Design &#58; A Customizable Message Logger in C++
date: 2024-02-04 20:00:00
description: Message logger offers three policies&#58; directing log messages to different outputs (such as console or file), adding optional message stamps (like timestamps), and supporting customization through callable functions (such as lambdas for specialized message formatting).
tags: generic_programming, code
categories: code
featured: false
---

Policy-based design is a versatile design pattern that underpins many useful features in modern C++, such as smart pointers. 
Despite its resourcefulness, I never delved into it during my work developing scientific codes.Intrigued by its potential, I decided to explore its application by creating a simple message logger that could be customized to suit user needs. 

Before diving into the specifics, let's review the context in which this design pattern shines.

<span style="font-size: larger;"><b>**Overview**</b></span><br>
Policy-based design serves as a compile-time counterpart to the well-known strategy pattern.
It may be useful in scenarios where when we know the desired behavior ('what'), but do not know a single, definitive implementation ('how'). In essence, it allows for multiple 'how' implementations, offering flexibility for possible future enhancements or changes in requirements.

This decoupling of desired behavior and implementation details enables developers to define various policies at compile-time, dictating different behaviors of a class.
This approach provides a high degree of customization and adaptability, exemplified by features such as 'std::unique_ptr' allowing users to define custom deleters.

<span style="font-size: larger;"><b>**Message Logger Example**</b></span><br>
Imagine we are developing a complex code, and we require an extensive logger to track the execution flow and to diagnose issues. We would like a message logger that can:
- direct messages to different destinations such as console or a user-defined file
- prepend the log message with some message stamp, such as a timestamp or a process id
- allow users to pass custom callables for logging special data types or performing additional tasks

<span style="font-size: larger;"><b>**Implementation Policies**</b></span><br>
To achive these objectives, we will create 'MsgLogger' class with the following policies:

1. StreamPolicy: dictates where the log messages will be directed. Implementations:
   - WriteToConsole: sends messages to the console.
   - WriteToFile: writes messages to a file (with support for thread safety).

2. StampPolicy: controls the formatting of prepended message stamps.
   - NoStamp: opts for no stamp inclusion.
   - WithStamp_TimeSecPrecis: prepends a timestamp to each log message.
   - WithStamp_TimeMicroSecPrecis: prepends a timestamp with microsecond precision.

3. CallablePolicy: determines whether a callable can be passed to the logger object.
   - NoCallable: opts not to support a callable.
   - WithCallable: allows users to pass a user-defined callable such as a lambda function.

<span style="font-size: larger;"><b>**Implementation Details**</b></span><br>
The 'MsgLogger' logger class combines these policies to offer a flexible solution.
Users can combine these policies according to their need or add new implementations of these policies independently, making code reusable and maintainable.
```c++
template <typename StreamPolicy=WriteToConsole,
          typename StampPolicy=NoStamp,
          typename CallablePolicy=NoCallable>
class MsgLogger : private StreamPolicy
{
public:
    /* Preference for r-value references.
     * l-value strings must be moved while passing as arguments.
     */
    explicit MsgLogger(std::string&& init_msg = "", 
                       StreamPolicy&& stream_policy = StreamPolicy()) :
    StreamPolicy(std::move(stream_policy))
    {
        StreamPolicy::operator()("\n" + StampPolicy::get_stamp() + init_msg);
    }

    /*default F is an empty function */
    template <typename F=std::function<void()>, typename... Args>
    void operator() (std::string msg, F&& func={}, Args&&... args)
    {
        std::string callable_duration= std::string{};

        //forward callable signature and arguments
        CallablePolicy::call(callable_duration, std::forward<F>(func), 
                                                std::forward<Args>(args)...);

        StreamPolicy::operator()(StampPolicy::get_stamp() + msg + callable_duration);
    }

   ~MsgLogger() = default;

    //non-copyable
    MsgLogger(const MsgLogger& that) = delete;
    MsgLogger& operator=(const MsgLogger& that) = delete;

    //movable, let's say
    MsgLogger(MsgLogger&& that) :
        StreamPolicy(std::move(that)) {}

    MsgLogger& operator=(MsgLogger&& that) noexcept
    {
        if(this == &that) return *this;
        StreamPolicy::operator=(std::move(that));
        return *this;
    }
};
```

In our design, StreamPolicy is inherited by MsgLogger instead of composed. This choice of inheritance, marked as private, emphasizes that MsgLogger is not a StreamPolicy but is only implemented-in-terms-of it.

For instance, the simplest StreamPolicy, WriteToConsole, is defined as follows:
```c++
struct WriteToConsole
{
    void operator() (const std::string& msg) {
        std::cout << msg << "\n";
    }
};
```
Inheriting StreamPolicy offers an advantange when the base class, such as WriteToConsole, has no data members. In such cases,  compilers optimize the size of the derived class object (MsgLogger object) through 'empty base class optimization'.

A simple 'WithStamp_TimeMicroSecPrecis' policy is given below:
```c++
struct WithStamp_TimeMicroSecPrecis
{
    static std::string get_stamp()
    {
        auto timestamp = std::chrono::high_resolution_clock::now();
        uint64_t micros_since_epoch = std::chrono::duration_cast<std::chrono::microseconds>(
                timestamp.time_since_epoch()).count();

        auto microseconds = micros_since_epoch % 1000000;
        auto time_now = std::chrono::system_clock::to_time_t(timestamp);

        std::ostringstream oss;
        oss << std::put_time(std::localtime(&time_now), "%T")
            << "." << std::setw(6) << std::setfill('0') << microseconds;
        return "[" + oss.str() + "] ";
    }
};
```
This policy is stateless, as it doesn't require any data members. Additionally, the 'get_timestamp' function operates independently of the message logger object, allowing us to use it statically without the need to store it within the MsgLogger class.

The 'WithCallable' policy is outlined below:
```c++
struct WithCallable
{
    template<typename F=std::function<void()>, typename... Args>
    static void call(std::string& duration, F&& func={}, Args&&... args)
    {
        //convert func to std::function object and test
        if (std::function<void(Args...)>(func))
        {
            /* func can be invoked.
             * measure duration, output as string.
             */
            auto start_time = std::chrono::high_resolution_clock::now();

            std::invoke(std::forward<F>(func), std::forward<Args>(args)...);

            auto end_time = std::chrono::high_resolution_clock::now();
            auto time_elapsed = std::chrono::duration_cast<std::chrono::microseconds>
                                (end_time - start_time);

            std::ostringstream oss;
            oss << time_elapsed.count();
            duration = " Time taken: " + oss.str() + " micro-sec.\n";
        }
        else {
            /* func is empty
             * attempting to invoke func will result in std::bad_function_call
             */
        }
    }
};
```
This implementation allows for invoking the forwarded callable and measuring its time. 
Additionally, it checks if 'func' is empty to handle cases where no callable is provided. 

<span style="font-size: larger;"><b>**Demonstration**</b></span><br>
Let's demonstrate how to use the 'MsgLogger' class with different combinations of policies.
```c++
int main()
{
    //default logger
    MsgLogger logger("Hello, this is default  logger!");
    logger("default logger is logging...");

    //file logger without callable
    MsgLogger<WriteToFile, WithStamp_TimeMicroSecPrecis>
        file_logger{"Hello, this is file_logger!", WriteToFile("file.dat")};
    file_logger("file_logger is logging...");

    //file logger with callable
    MsgLogger<WriteToFile, WithStamp_TimeMicroSecPrecis, WithCallable> callable_logger
    {"Hello, I am callable_logger!", WriteToFile("file.dat")};

    std::vector<std::complex<int>> complex_vec = { {1, 2}, {3, 4}, {5, 6} };
    log_vector(callable_logger, complex_vec);
}
```
where 'log_vector' is:
```c++
template<typename LoggerType, typename VecType>
void log_vector(LoggerType& logger, const std::vector<VecType>& vec)
{
    logger("Vector is printed! ", [&]() {
        int i=0;    
        for (auto& v: vec)
        {
            std::ostringstream oss;
            oss << " vec[" << i << "]: " << v.real() << " + " << v.imag() << "i";
            logger(oss.str());
            i++;
        }
    });
}
```

The output written to console reads:
````dat

Hello, this is default  logger!
default logger is logging...
````

The output written to 'file.dat' reads:
````dat

[10:28:07.134316] Hello, I am callable_logger!
[10:28:07.134321]  vec[0]: 1 + 2i
[10:28:07.134322]  vec[1]: 3 + 4i
[10:28:07.134324]  vec[2]: 5 + 6i
[10:28:07.134326] Vector is printed!  Time taken: 5 micro-sec.


[10:28:07.134284] Hello, this is file_logger!
[10:28:07.134306] file_logger is logging...
````
<span style="font-size: larger;"><b>**More**</b></span><br>
We have only scratched the surface of the power of this design pattern.
Policies can extend beyond simple function objects and include template instantiations or even templates themselves. 
Additionally, policy classes can be composed rather than inherited, which is useful if policies hold a state and need to be stored in the policy class.

Combining policy-based design with the Curiously Recurring Template Pattern (CRTP) allows for modifications to the public interface of a class.
Moreover, this pattern can be leveraged to create policies for writing unit tests, enabling robust testing strategies.

An interesting application of policy-based design involves combining it with the type-erasure technique to unify different customized instances of the policy class into a single type, as seen in std::shared_ptr.

However, it's important to note a major criticism of this design pattern. When dealing with many policies, specifying a non-default policy towards the end of a long policy list requires declaring all policies before it. 
While this challenge can be mitigated by organizing policies thoughtfully and using policy adapters with aliases, it is worth considering in other possible design patterns that may be used instead.
 
For further exploration and reading, please refer to:
- [Code](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/design/policy_based_design/ex1_msglogger.cpp) described in this blog 
- **Hands-On Design Patterns with C++** by Fedor Pikus
- **Modern C++ Design: Generic Programming and Design Patterns Applied** by Andrei Alexandrescu
- [Policy-based design in C++20](https://www.youtube.com/watch?v=fauJG1WEDUM) by Goran Arandjelović on Youtube
- [The problem with policy-based design](https://www.foonathan.net/2017/02/policy-based-design-problem/) by Jonathan Müller
- [Policy based approach with logger](https://stackoverflow.com/questions/20276659/policy-based-approach-with-logger) on stackoverflow
- **ChatGPT** (with a grain of salt)
