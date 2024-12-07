---
layout: post
title: C++ Template Basics
date: 2024-05-25 12:00:00
description: This covers basics of templates including function templates, class templates, variadic templates, non-type template parameters, and more.
tags: C++ templates
categories: code
featured: false
datatable: true
featured: false
---

### Function Templates

| Code || Brief Information |
| :--------------- || :---------------- |
| [type_conversion](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/templates/function_templates/type_conversions/ex1.cpp) || Type conversion during argument type deduction. Use of `<boost/type_index.hpp>`. |
| [return_type_deduction](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/templates/function_templates/return_type_for_multiple_template_params/ex1.cpp) || Three ways to deduce return type in a template functions, differences in C++11, 14, 17 |

<br>

### Class Templates

| Code || Brief Information |
| :--------------- || :---------------- |
| [template_entity](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/templates/class_templates/friend/ex1_template_entity.cpp) || What is template entity? -- an ordinary function instantiated with a class template. |
| [nonmember_FT](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/templates/class_templates/friend/ex2_nonmember_function_template.cpp) || How to declare a template entity? -- implicity declaration of new function template |
| [forward_declare_FT](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/templates/class_templates/friend/ex3_forward_declare_function_template.cpp) || How to declare a template entity? -- forward declaration of FT for the class and inside the class declare specialization of nonmember function template |

<br>

### Variadic Templates

| Code || Brief Information |
| :--------------- || :---------------- |
| [example1](https://github.com/saurabh-s-sawant/cpp_exercises/blob/main/templates/variadic_templates/example1.cpp) || Simple recursive print function. Difference in using `sizeof...`operator with regular if versus compile-time if. |

<br> More examples with detailed comments will be added in time.

#### References

- [C++ Templates: The Complete Guide (second ed.)](https://www.amazon.com/C-Templates-Complete-Guide-2nd/dp/0321714121) by Vandevoorde D., Josuttis N., Gregor D.
- [Back to Basics: Templates in C++](https://www.youtube.com/watch?v=HqsEHG0QJXU&t=11s) by Nicolai Josuttis.
