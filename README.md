#pomelo-bt - behavior tree for node.js

pomelo-bt is a Behavior-Tree module for pomelo project to implement AI. More information about Behavior-Tree please refer other articles from Internet, such as [Understanding Behavior Trees](http://aigamedev.com/open/article/bt-overview/).

+ Tags: node.js

##Installation
```
npm install pomelo-bt
```

##Behavior tree nodes

###Node
The base class of all the behavior tree node classes. Its constructor receives a blackboard instance as parameter.

Each node class provides a `doAction` method to fire the behavior of current node instance. All the children should implement their own `doAction`. And the `doAction` method sould report a result code to the parent when it return. The result code is one of below:

+ RES_SUCCESS: the behavior finished successfully.
+ RES_FAIL: the behavior fails.
+ RES_WAIT: the behavior is running and should be continued in next tick.
The parent node makes its decision based on the result code.

###Composite
The base class of all the composite nodes. A composite node has arbitrary child nodes and it has a `addChild` method to add child node.

###Decorator
The base class of all the decorator nodes. A decorator node has the ability to decorate the result for its child node. A decorator node has only one child node and has a `setChild` method to set the child node.

Followings are some behavior node types provided in `pomelo-bt`.

##Composite nodes
###Sequence
Implementation of `sequence` semantics.
####Sequence(opts)
+ opts.blackboard - blackboard instance for the behavior node.

###Parallel
Implementation of parallel semantics.
####Parallel(opts)
+ opts.blackboard - blackboard instance for the behavior node.
+ opts.policy - Failure strategy for Parallel node: `Parallel.POLICY_FAIL_ON_ONE`(default) return `RES_FAIL` if one child node fail, `Parallel.POLICY_FAIL_ON_ALL` return `RES_FAIL` only on all the child nodes fail.

###Selector
Implementation of selector semantics.
####Selector(opts)
+ opts.blackboard - blackboard instance for the behavior node.

###Decorator nodes
###Loop
Implementation of loop semantics.
####Loop(opts)
+ opts.blackboard - blackboard instance for the behavior node.
+ opts.child - child node for the decorator node。
+ opts.loopCond(blackboard) - loop condition function. return true to continue the loop and false to break the loop.

###Condition
Return `RES_SUCESS` if the condition is true otherwise return `RES_FAIL`.
####Condition(opts)
+ opts.blackboard - blackboard instance for the behavior node.
+ opts.cond(blackboard) - condition function, return true or false.

##Other nodes
###If
Implementation of loop semantics. If the condition is true, then fire the child node.
####If(opts)
+ opts.blackboard - blackboard instance for the behavior node.
+ opts.action - child node.
+ opts.cond(blackboard) - condition function, return true or false.

##Usage
``` javascript
var util = require('util');
var bt = require('pomelo-bt');
var Sequence = bt.Sequence;
var Node = bt.Node;

// define some action nodes
var HelloNode = function(blackboard) {
  Node.call(this, blackboard);
};
util.inherits(HelloNode, Node);

HelloNode.prototype.doAction = function() {
  console.log('Hello ');
  return bt.RES_SUCCESS;
};


var WorldNode = function(blackboard) {
  Node.call(this, blackboard);
};
util.inherits(WorldNode, Node);

WorldNode.prototype.doAction = function() {
  console.log('World');
  return bt.RES_SUCCESS;
};

var blackboard = {};

// composite your behavior tree
var seq = new Sequence({blackboard: blackboard});
var hello = new HelloNode(blackboard);
var world = new WorldNode(blackboard);

seq.addChild(hello);
seq.addChild(world);

// run the behavior tree
seq.doAction();
```