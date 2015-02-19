# TodoFire

####Objectives
Because the project today is rather large, this mini project will be short. We're going to take the first Mini Project we did (basic todo list), and add firebase to it to persist our list. 

####Step 1: Clone Todo List
* Head over to your Mini Project from Day 1 (Todolist) and clone it. 
* Once you clone it cd into the directory and run ```rm -rf .git``` to remove all the old git stuff.
* If you'd like, fork this proejct then use ```git remote add origin https://github.com/USERNAME/react-mini3-todofire``` to link your code with github.
* Run ```npm install``` to install all the proper dependencies.
* Run ```npm install --save firebase``` to add firebase as a dependency. 
* run ```webpack -w``` to watch our file for changes.

####Step 2: Create a firebase
* Head over to firebase.com and sign up.
* Create a new project calling it whatever you like. 

####Step 3: Add Firebase Ref.

The very first thing we want to do is add a reference to our Firebase when our main component which is handling our todo list mounts.

* In the ```ListContainer``` component add a ```componentDidMount``` method which has the following things inside it
  - create a reference to your new firebase *using new Firebase(YOUR-URL)* and save that to a variable called ```firebaseRef``` that lives on the component's *```this```* object. 
  - Now, use that ref we just made to invoke the ```.on``` method and pass it ```child_added``` as well as a callback function which has snapshot as its only parameter.
  - Inside the callback function use ```setState``` to add ```snapshot.val()``` to our state's ```list``` array. *hint: remember, don't modify state directly. Though it seems weird, it's pretty common practice to use ```.concat``` inside of ```this.setState``` to modify the state.

The componentDidMount method should now look similar to this. 
```javascript
componentDidMount: function(){
  this.firebaseRef = new Firebase("https://reactweek-todofire.firebaseio.com/todos");
  this.firebaseRef.on('child_added', function(snapshot){
    this.setState({
      list: this.state.list.concat([snapshot.val()])
    })
  }.bind(this));
}
```

Now what will happen is the callback function will run for every item that's at the /todos endpoint as well as anything another item gets added there.

####Step 4: Handle Add Item

The next method we need to modify is the handleAddItem method. Right now it's just concerned with the local ```list``` state. However, we want it to be concerned with our actual firebase database. 

* Remove ```this.setState``` from ```handleAddItem``` and instead use the ```.push()``` method that's on the firebase ref you created earlier to push the new item into your firebase.

Your code should look like this.

```javascript
handleAddItem: function(newItem){
  this.firebaseRef.push(newItem);
}
```

Pretty slick right? We don't need to worry about updating the list because our ```child_added``` callback we added to ```componentDidMount``` is doing all that for us.

####Step 5: Handle Remove Item
This method is going to be almost exactly the same except for one minor addition.

* After you invoke ```.splice```, add a line that uses the firbase property ```.set()``` passing in ```newList``` to let firebase know about the updates. 

Your code should look like this.

handleRemoveItem: function(index){
  var newList = this.state.list;
  newList.splice(index, 1);
  this.firebaseRef.set(newList);
  this.setState({
    list: listRef
  })
}

and that's it. With the compartmentalization of React we only need to modify one component (where the data actually lives) and everything else remains the same.  
