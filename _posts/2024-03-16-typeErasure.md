---
layout: post
title: Exploring Type Erasure as a Design Pattern &#58; A Generic Materials Solver
date: 2024-01-11 21:00:00
description: Exploring possibility of using type erasure as a design pattern through an example of a generic materials solver. 
tags: generic_programming
categories: code
featured: false
---

<span style="font-size: larger;"><b>**Problem Statement / Motivation**</b></span><br>
Consider this problem: we want to design a materials solver that computes the transport of electrons through different materials. Let's say we opt for a super accurate numerical method, like quantum transport based on Schrodinger's equation.

This method involves many steps and details, and we want our code to handle various materials. Implementing the method for each material could vary depending on the material's data structure and physics. So, how should we design this code?

My initial thought is to use inheritance — have a base class defining an interface for the algorithm, with some default implementations that derived material classes can override. The base class can be a template class where the template parameter defines the relevant data structure, and the materials are specializations of this class template. We can store instantiated materials as pointers to the base class and use dynamic polymorphism to perform the algorithm. Although there are more nuances to this setup, let's keep it simple. This design allows for extending to different materials as well as modeling multiple copies of a given material together.

However, what if there are multiple possible implementations for one of the steps of the algorithm? Creating another derived class for each material isn't viable, as it leads to an intractably deep inheritance hierarchy, too many derived classes, and duplication.

The typical, well-tested solution to this problem is the Strategy Design Pattern, as explained in the Gang of Four book, or described beautifully on [refactoring.guru](https://refactoring.guru/design-patterns/catalog).

The Strategy design pattern abstracts implementation details into a strategy for executing a particular operation, such as step 1 of the algorithm. This operation can have multiple implementations without modifying the base class. This allows us to interchange the object for implementing the operation at runtime. This design results in three architectural levels: high-level, composed of the base class defining the algorithm's interface; intermediate level, where we define different materials and corresponding strategies for their operations; and the low level, which contains all implementation details such as concrete strategies.

However, what if someone else on our team wants to develop a different, perhaps lower-fidelity numerical method (e.g., a Boltzmann transport method) for material transport, also capable of handling different materials within the same general framework for material transport?

Clearly, we don't want material classes to depend on a particular method defined previously as the base class. If we do that, we'll end up duplicating a good chunk of the code that defines material characteristics.

So, what is the best design to handle this?

Thankfully, a few months ago, I discovered Klaus Iglberger on the internet through his engaging lectures at CppCon. In his lecture, "[Breaking Dependencies: Type Erasure - A Design Analysis](https://www.youtube.com/watch?v=4eeESJQk-mw&t=2889s)," he described the use of type erasure as a design pattern. He used an example of shapes to illustrate this pattern, which combines three design patterns: external polymorphism, bridge, and prototype, to achieve something remarkable.

His example of shapes resonated a lot with the materials problem I described above, and more importantly, the problem we all encounter a lot in scientific code development: dealing with dependencies. Code components become too coupled to each other for any meaningful extension to happen without breaking the Open/Closed principle.

Following his video, here is the dummy code I wrote. If I had come across this two years ago, I would have made different decisions in my own development. But hey, there will be many opportunities to code. So, I consider myself fortunate to have come across this. I hope you do too.

(On a side note) Althogh the Devin AI is learning to code, I highly doubt it would discover such design strategies on its own. So, the idea of AI replacing developers remains more of a wishlist for now.

<span style="font-size: larger;"><b>**Type Erasure in Action**</b></span><br>
I would jump straight to main() and demonstrate what we can do with type erasures. 
I can define a vector of materials that follow the steps of my particular algorithm, execute these steps polymorphically (that's what external polymorphism let's you do), completely decouple material details from the operations that may be performed on the material, and have flexibility for multiple implementations of those operations.

```c++
int main() 
{
    /* Materials that we would Algorithm to be executed */
    using Materials = std::vector<Algorithm>;

    Materials materials;

    /* Each material is instantiated with its own constructor.
     * Here, the constructor argument defines number of unitcells.
     */
    materials.emplace_back( CNT{4} ); 
    materials.emplace_back( Graphene{2} );

    computeAlgorithm(materials);
}

void computeAlgorithm(std::vector<Algorithm> const& materials) 
{
    /* Polymorphism in action. 
     * step1 is executed for each material.
     */
    for(auto const& material: materials) {
        computeStep1(material);
    }
}

void computeStep1(Algorithm const& material) 
{
    /* Actual computations are delegated to */    
    material.pimpl->computeStep1();
}
```
Here is the Algorithm class where all the magic happens.

```c++
class Algorithm {
private:
    /* External polymorphism design pattern: allows C++ classes
     * unrelated by inheritance and/or having no virtual methods to be
     * created polymorphically.
     * We create a interface, AlgorithmConcept, and 
     * a derived class template, AlgorithmModel.
     * Algorithm model has a constructor that takes some T and uses it.
     */
    struct AlgorithmConcept {
        virtual ~AlgorithmConcept() {}
        
        /* Steps of this algorithm concept, such as this,
         * are the affordances required by the derived class.
         */
        virtual void computeStep1() const = 0;
        
        /* Prototype design pattern: clone function will return a copy of
         * whatever stored in the derived class in the form of a unique_ptr
         * to AlgorithmConcept.
         */
        virtual std::unique_ptr<AlgorithmConcept> clone () const = 0;       
    };

    template<typename T>
    struct AlgorithmModel : public AlgorithmConcept {
        AlgorithmModel(T&& args) : object{std::forward<T>(args)} {}
        
        std::unique_ptr<AlgorithmConcept> clone() const override {
            return std::make_unique<AlgorithmModel>(*this);
        }

        void computeStep1() const override {
            ::computeStep1(object); // Affordances required by type T
        }
        
        T object;
    };

    /* friend functions */
    friend void computeStep1(Algorithm const& material);

    /* See that in the constructor, we store object we get as pointer to base.
     * That means we type erasure what we store.
     */
    std::unique_ptr<AlgorithmConcept> pimpl = nullptr;

public:
    /* This templated constructor acts as a bridge:
     * It instantiates algorithm model for whatever T object you pass
     * and we store it as pointer to base so we have erased the type!!
     * We can have infinite number of derived classes but we dont have to
     * write them, the compiler will generate them for us!!
     */
    template<typename T>
    Algorithm(T&& args) : pimpl{ std::make_unique<AlgorithmModel<T>>(std::forward<T>(args)) } {}

    /* How to copy an object when we have erased the type of the object? 
     * Using clone functions! Prototype design pattern.
     */
    Algorithm(Algorithm const& that) {
        if (that.pimpl) {
            pimpl = that.pimpl->clone();
        } else {
            pimpl.reset(); // Reset pimpl to nullptr if 'that' has no pimpl
        }
    }
    Algorithm& operator=(Algorithm const& that) noexcept {
        if (this != &that) {
            if(that.pimpl) {
                auto temp = that.pimpl->clone();
                std::swap(pimpl, temp);
                temp.reset();
            }
        }
        return *this;
    }

    /* Move constructor is straighforward to implement. */
    Algorithm(Algorithm&& that) = default;
    Algorithm& operator=(Algorithm&& that) noexcept;
};
```


<span style="font-size: larger;"><b>**More**</b></span><br>
For details, please refer to:
- Complete [Code](https://github.com/saurabh-s-sawant/cpp_exercises/tree/main/design/type_erasure) described in this blog.
- [Breaking Dependencies: Type Erasure - A Design Analysis](https://www.youtube.com/watch?v=4eeESJQk-mw&t=2889s) by Klaus Iglberger (Absolutely amazing!)
- [Breaking Dependencies - C++ Type Erasure - The Implementation Detail](https://www.youtube.com/watch?v=qn6OqefuH08&t=1381s) by Klaus Iglberger (another relevant talk).
- **Hands-On Design Patterns with C++** by Fedor Pikus for details on type erasures.
