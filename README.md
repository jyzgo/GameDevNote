####About this Blog

Hi,My name is Eason Jiang,And this's the first time I write tech article in English.  I used be a game designer , and for now I'm a cocos2dx game developer 


Rencently, I'm interseting in Unity 3D,because I believe it's a more proficent technology than Cocos2dx is.


I believe the best way to learn a tech is try to teach it to someone else. and this the reason I build this Blog.
So,talk is cheap,let's code sth.

##Button and Spin

Ok, In this tutorial our target is to create an cube and a button. 
When we click the button, the cube spinning
===================
The first step is create a new project.

blablabla 
After that ,we create a plane as background

Step 3, We create a cube in object

And we select the cube and add a C# script on it, name it as  "Spin.cs"

Double click it, and enter the follow code

	void OnGUI(){
		if (GUI.Button (new Rect (100, 170, 200, 100), "Spin The Cube")) {
			transform.Rotate(Vector3.up * Time.deltaTime * (-100));
		}

	}

This's a little bit werid from my pov.In cocos2dx, or some other game dev engine,If we want to create some UI, there's step to do it.
Step 1: init the button
Step 2: update the call back of the button,some time this step is already done in step one.

So let's do some experiment,let's modify the code in OnGUI() like the follow:

	
	//original x
	private float movx = 1;
	void OnGUI(){

		movx = movx +  0.01f;
		if (GUI.Button (new Rect (movx, 170, 200, 100), "Spin The Cube")) {
			transform.Rotate(Vector3.up * Time.deltaTime * (-100));
		}

		Debug.Log ("On GUI");
	}

After we run it, the console displays the results, So many

"On GUI"
"On GUI"
"On GUI"
"On GUI"
"On GUI"
....

Meanwhile, the button continuously move to the right, so we may safely arrive at the following conclusion 

1.The function OnGUI() is a game update function,it'll be updated at regular intervals
2.There's no init function (for now),so unity just draw a new button every interval.

So we deduce that every update function work like OnGUI(), if you want to use these like old way,you have to define them out of these functions.


=======
Best practioe Unity Sublime plugin

Unity Completion Light

=====

Let's create a plane as game background, and modify it's coordinates to origin position,that means modify it's transform postion to (0,0,0),and move our camera to make sure the plane in our view;
	Tips:
		Alt +  mouse left, scene view rotate with the ground
		Alt +  Ctrl(Com) + left, scene view move translation
		Mouse wheel:Move the view forth and back
	Tips End

Next feature, let's try to make the cube jump.
First,modify the cube's postion,drag the handle on it to move to the corresponding direction
And we still need a sphere, create it in game object.

####Add rigidbody

Now add a Rigidbody to both of them, to make sure that they have mass and affected by gravity
and then lift the cube to the air,after that when we press the Play button,the cube will fall firstly.

####Make the cube jump

Now it's has a rigidbody, add the follow code to the Update()

	void Update () {
		if(Input.GetMouseButtonDown(0)){
			this.GetComponent<Rigidbody>().velocity = Vector3.up * this.jump_speed;
		}
	}
After this, each time when we press the mouse left button,it'll get a velocity upward,and the cube jump once

####Add material

In project view, press mouse right and create a Material ,and change it's name to Player Material,and modify it's 
color to what ever you want, and drag it to any gameObject that have some Render

####Add light 
Light is a kind of GameObject,create one and drag it to any postion you want, you won't miss it.

###Send the ball fly

First, add a rigidboy to the ball.
Second,add a script to it and add the follow in the Start() function

	// Use this for initialization
	void Start () {
		this.GetComponent<Rigidbody>().velocity = new Vector3(-0.8f, 8.0f , 0.0f);
	
	}

Now the ball will fly like a parabola

###Organize project folder
Create folder in project view and drag everything according classfication :Prefab,Scene,Script,Material

###Create a Launcher Script

Let's try to fire some ball.

Create a empty GameObject and create a Script on the gameobject, name it Laucher.cs and add the follow code in it

	public class Launcher : MonoBehaviour {

		public GameObject ballPrefab;

		// Use this for initialization
		void Start () {
		
		}
		
		// Update is called once per frame
		void Update () {
			if(Input.GetMouseButtonDown(1)){
				Instantiate(this.ballPrefab);
			}
		
		}
	}


Now drag ball prefab in to inspector Ball Prefab slot and run the game
Each time we press the right button, a ball prefab be Instantiated

####Delete the ball out of view

If we keep clicking the right mouse, there'll be full of balls in this scene,and
even they leave the scene, they still exist. 
So let's try remove them when they're not visible.

	void OnBecameInvisible(){
		Destroy(this.gameObject);
	}

And when they out of the view ,they'll be destoryed

## Forbide the cube jump twice in the air

For now, when we press the mouse left, the cube jumped. That is not what happened in real life,

Let's add the follow code

public class Player : MonoBehaviour {

	protected float jump_speed = 5.0f;
	private bool is_landing = false;
	// Use this for initialization
	void Start () {
		is_landing = false;	
	}
	
	// Update is called once per frame
	void Update () {
		if (is_landing) 
		{
			if(Input.GetMouseButtonDown(0)){
				this.GetComponent<Rigidbody>().velocity = Vector3.up * this.jump_speed;
				is_landing = false;
			}
		}

	}

	void OnCollisionEnter(Collision collision)
	{
		is_landing = true;
		
	}
}

What we do in the previous code?
We define a bool vale is_landing to record the cube's landing state,
each time the cube jumped, the is_landing set false,and when the cube collide anything, the
is_landing state will be set true,and only when the is_landing state
is true, the jump function will be executed.

But there's bug in it,

Let's fix it.

Firstly , add a Debug.Break() in Update()
This function will pause the game when be called,and after that, we can play game each frame
And we find the collision problem, when cube collide anything,the is_landing state will be set true,and the cube
be able to jump again.
And the fix plan is Add "Tag" to the prehab, make sure only when the plane collide, the state will be reset

Set the Floor tag as "Floor", and add the follow code 
	void OnCollisionEnter(Collision collision)
	{
		if (collision.gameObject.tag == "Floor") 
		{
			is_landing = true;	
		}
	}

So only when the cube collide the floor, it's landing state will be reset to ture


####Which Script run first?

As we already know,the gameobject can add many script on it, but which one run firstly? or just random?
Let's do a little experiment
Create an empty gameObject and add one script on it,

public class Test1 : MonoBehaviour {

	private bool isUpdated = false;
	// Use this for initialization
	void Start () {
		Debug.Log("test 1 start");
	}
	
	// Update is called once per frame
	void Update () {
		if (!isUpdated) 
		{
			Debug.Log("test 1 up");
			isUpdated = true;
			
		}
	}
}

As we see, This script just output some log, when Start(),and first time Update(),now let's add another one on it

public class Test2 : MonoBehaviour {

	private bool isUpdated = false;
	// Use this for initialization
	void Start () {
		Debug.Log("test 2 start");
	
	}
	
	// Update is called once per frame
	void Update () {
		if (!isUpdated) 
		{
			Debug.Log("test 2 up");
			isUpdated = true;
			
		}
	
	}
}

Basically same funcionality,but output content is "test 2.."
Run the game,and the Console output

test 2 start
test 1 start
test 2 up
test 1 up

The conclusion is quite clear, Unity will run the script according to the reverse chronological time order.


###Freeze Rotation

The cube's rigidbody has a option name "Constrains",unfold it and check the boxes that you want to freeze

###Set the bounciness

Set the Ball prefab's mass to 0.01 in it's rigidbody, and apply it, now every ball's mass is seted to the same mass
But that doesn't look very good, so let's try the Physic Material

In project view, press right mouse and Create->Physic Material and name it Ball Physic Material
Select that and change it's Bounciness to 1, and drag this Physic Material to Ball's Sphere Collider->Material,
and apply it.
Now, when they collide, the ball bounced quickly

###Set the gravity

Edit->Project Setting->Physics ,change the Gravity y to -20, then cube and balls falls more quickly.





