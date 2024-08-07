**This library is deprecated! Read how to migrate at [https://eloquentarduino.com/posts/tensorflow-lite-tinyml-esp32](https://eloquentarduino.com/posts/tensorflow-lite-tinyml-esp32).**

# EloquentTensorFlowCortexM

An Arduino library to run TensorFlow models on ARM Cortex-M chips without pain.

## How to use

Once you have your TensorFlow model exported in a C header format (for example using `xxd`),
running it is as easy as:

```cpp
/**
 * Run a TensorFlow NN to predict sin(x)
 * For a complete guide, visit
 * https://eloquentarduino.com/tensorflow-lite-arduino-nano
 */
#include <eloquent_tensorflow_cortexm.h>
// replace with your own model
#include "sine_model.h"
// replace with the correct number of ops
#define NUM_OPS 1
// this is trial-and-error
// when developing a new model, start with a high value
// (e.g. 10000), then decrease until the model stops
// working as expected
#define ARENA_SIZE 2000

using Eloquent::CortexM::TensorFlow;

TensorFlow<NUM_OPS, ARENA_SIZE> tf;

/**
 * 
 */
void setup() {
    Serial.begin(115200);
    delay(3000);
    Serial.println("__TENSORFLOW CORTEX-M SINE__");

    // replace with the correct values
    tf.setNumInputs(1);
    tf.setNumOutputs(1);
    // add required ops
    tf.resolver.AddFullyConnected();

    while (!tf.begin(sine_model).isOk()) 
        Serial.println(tf.exception.toString());
}


void loop() {
    float x = (millis() % 1000) / 1000.0f * 3.14;
    float input[1] = {x};

    while (!tf.predict(input).isOk())
        Serial.println(tf.exception.toString());

    Serial.print("x = ");
    Serial.print(x);
    Serial.print(", sin(x) = ");
    Serial.print(sin(x));
    Serial.print(", y = ");
    Serial.println(tf.result());
    delay(100);
}
```
