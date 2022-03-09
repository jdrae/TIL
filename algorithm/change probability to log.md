# Change probability to log

### What for? 
When multiplying probabilities, which is real numbers, the result could be so big to cause **underflow**. 

### Solution
The simple solution is to change probability to its **log**, and add others instead of multiplying. After adding all the probability to the result, we might change the log to float(or double), and just check the underflow just one last time.

### Code example (C++)

	#include <iostream> //cin
	#include <cmath>//log, exp
	using namespace std;
	
	double p1, p2;
    cin >> p1, p2;
    p1 = log(p1);
    p2 = log(p2);
    double resultInLog = p1 + p2;
    double resultInP = exp(resultInLog);
    

### Be careful
* In **C**, `double log(double n)` takes only double type of variable. If you want to use float type, use `float logf(float n)`. (If you use **C++** it doesn't matter. Use `log()` for both.)
* As probability is represented the number between [0,1], the log of the number would be **negative to 0**.
