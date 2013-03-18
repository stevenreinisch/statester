statester - Complex iOS User Interfaces in Minutes
==================================================

Do you ever have the smell of a good italian restaurant in your nose when you enter your workspace?
You take out your laptop, put it on your desk, and realize that there is a spaghetti smell coming out
of it. And then it hits you! It's the code of your iOS app. Every premium italian cook envies your 
coding style - not even his mother can make better spaghetti!

Seriously, way too often I found myself in situations in which my code became totally unmaintainable.
Especially when I implemented views that contained a lot of subviews that must be represented in 
different styles depending on what the user did do previously. Since it is hard to find the best
way to solve a given interaction problem with the very first coding session, I have to take multiple
iterations. Within each iteration I add an if-statement here and there, and in the next iteration
every if-statements grows further leading to something like this:

	-(void) onSendButtonPressed: (id)sender {
	
		if ((_numberSet && _tableFieldNotEmpty) || (!_numberSet && _selectedItem)) {
		
			_upperBarButton.hidden = NO;
			_amountField.text = _selectedItem.amount;
			_subview.statusBar.background = RED;
			//...
			
		} else if ((_numberSet || !_tableFieldNotEmpty) && (_numberSet && _check4.activated)) {
			_upperBarButton.hidden = YES;
			_amountField.text = @"";
			_subview.statusBar.background = GREEN;
			//...
			
		} else if (_tableFieldNotEmpty || !_check1.activated) {
		
			_upperBarButton.hidden = YES;
			_subview.statusBar.background = YELLOW;
			//...
		}
	}

	- (void) onNavigationbarTap:(UITapGestureRecognizer *)gestureRecognizer {
	
		if ((_numberSet && _tableFieldNotEmpty) || (!_numberSet && _selectedItem)) {
		
			_activityIndicator.hidden = NO;
			//...
			
		} else if ((_numberSet || !_tableFieldNotEmpty) && (_numberSet && _check4.activated)) {
			
			_activityIndicator.hidden     = YES;
			_amountField.text             = @"";
			_subview.statusBar.background = GREEN;
			//...
			
		} else if ...
		}
	}

…. puhh! Try to change your UI quickly with this code. Better call your wife that you will order a pizza in the office tonight.

I realized that all my UI elements have a different appearance based on the state of the app. Each different state was handled 
multiple times in different methods. I asked myself if it would be possible to put all the code that changes the UI in a certain state into one
method without having to write these nasty if-statements over and over again. I imagined something like this:

	- (void)enteredState_INITIALIZED {
	
		_activityIndicator.hidden     = YES;
		_amountField.text             = @"";
		_subview.statusBar.background = GREEN;
	}

	- (void)enteredState_WAITING_FOR_RESPONSE {
	
		_activityIndicator.hidden     = NO;
		_amountField.activated        = NO
		_subview.statusBar.background = RED;
	}

The basic idea is to have a dedicated place in the code where I can set the appearance of the different UI elements
depending on the state the app is currently in. The evaluation of which state is currently active should be done
elsewhere.

While searching for a solution to be able to implement my UI code like this, I stumbled upon [Finite State Machines (FSMs)](http://en.wikipedia.org/wiki/Finite-state_machine). FSMs allow you to describe the state of an object as a set of states and the transitions among these states. 

The state of an app can easily be described by an FSM. But that's just a description and I need code that sets the appearance of UI elements - it should not just be a drawing! To be able to transform an FSM into code that sets the appearance of UI elements I came up with a textual language for FSMs that is translated into Objective C code. I called this language and the generator that translates an FSMs to code **"statester"**. Technically speaking,
**statester** is a *Domain Specific Language (DSL)* and *Code Generator* for Finite State Machines.

The DSL is implemented on top of [Xtext](http://www.eclipse.org/xtext/) and [Xtend](http://www.eclipse.org/xtend/).
Xtext is used to implement the language's editor and parser. Xtend is used to implement the code generator. 

The code generator transforns a model of a finite state machine (FSM) into an Objective C representation. This representation is interpreted at runtime by an FSM interpreter. You provide the interpreter with a delegate that is called at predefined events in the lifecycle of the interpreted FSM. For instance, such an event is "entered state X", or "switched from state X to state Z triggered by the event E1". 

For instance you provide the interpreter with a class that implements the methods

	- (void) enteredState_INITIALIZED;

	- (void) enteredState_WAITING_FOR_RESPONSE;

and the interpreter calls these methods when these states are entered. You just tell the interpreter which event happended and the interpreter "switches" your FSM as described by the model of the FSM that you created with the statester language.

If you are interested in statester and want to use it, contact me and I will assist you in using it (and upload the code .. I was just too lazy to do that until now :) ).
