#Script Study

####Lift cycle

Let's introduce some important function in Unity
Look at the following code.

public class ScTest : MonoBehaviour {

	public int scriptIndex = 1;

	void Awake(){
		//Awake() be called when the script 'awake'
		Debug.Log("Awake  "+ scriptIndex);
	}


	// Use this for initialization
	void Start () {
		//Start() be called after awake
		Debug.Log("Start  "+ scriptIndex);
	}
	// Update is called once per frame
	bool isUpdted = false;
	void Update () {
		//Each frame, the Update() be called,So we set a flag to make sure this log printed only once time
		if (!isUpdted) 
		{
			isUpdted = true;
			Debug.Log("update  "+ scriptIndex);
		}
	}

	bool isLateUpdated = false;
	void LateUpdate(){
		//Each frame, after Update() be called
		if (!isLateUpdated) 
		{
			isLateUpdated = true;
			Debug.Log("Late update "+ scriptIndex);
			
		}
	}

	bool isFixedUpdated = false;
	void FixedUpdate(){
		//FixedUpdate() be called in fixed intervals
		//We can change intercal in "Edit->Project Settings -> Time ""
		//Mostly, move positon func will be set here
		if(!isFixedUpdated)
		{
			isFixedUpdated = true;
			Debug.Log("FixedUpdate "+ scriptIndex);
		}

	}

	bool isLogOnGUI = false;
	void OnGUI(){
		//Each frame called
		if (!isLogOnGUI) 
		{
			isLogOnGUI = true;
			Debug.Log("On GUI "+ scriptIndex);
			}

	}	

	void OnDestroy(){
		//Be called when this stript be 'Destroy'
		Debug.Log("Destroy "+ scriptIndex);
	}
}

Now, let's create a new cube and add the script on it.
Observe the output of console

"Awake 1"<br />
"Start 1"<br />
"FixedUpdate 1"<br />
"update 1"<br />
"Late update 1"<br />
"On GUI 1"<br />

So according to the log, we have already know the sequence of the invocation
But the FixedUpdate here is called base on interval,so this the different.

##Mutiple Script invocate sequence

The same object,let's add another script, but set it's scriptIndex as 2

"Awake 2"<br />
"Awake 1"<br />
"Start 2"<br />
"Start 1"<br />
"FixedUpdate 2"<br />
"FixedUpdate 1"<br />
"update 2"<br />
"update 1"<br />
"Late update 2"<br />
"Late update 1"<br />
"On GUI 2"<br />
"On GUI 1"<br />

add again as 3

"Awake 3"<br />
"Awake 2"<br />
"Awake 1"<br />
"Start 3"<br />
"Start 2"<br />
"Start 1"<br />
"FixedUpdate 3"<br />
"FixedUpdate 2"<br />
"FixedUpdate 1"<br />
"update 3"<br />
"update 2"<br />
"update 1"<br />
"Late update 3"<br />
"Late update 2"<br />
"Late update 1"<br />
"On GUI 3"<br />
"On GUI 2"<br />
"On GUI 1"<br />

As we see, the latest script called the first
and when we stop the game, we'll finde the OnDestroy() be called

"Destory1"<br />
"Destory2"<br />
"Destory3"<br />

The oreder of the calls is reversed

#Singleton 

Create an empty GameObject and create a new script name it GameManager



public class GameManager : MonoBehaviour {

	public static GameManager instance = null;
	void Awake()
		{
			if (instance == null) 
			{
				instance = this;
				
			}else if (instance != this)
			{
				Destroy(gameObject);
			}

		}

}

Previous code made it as a singleton, because the only way to create a new object in Unity is through
Instantiate method

#Create GameObject through script

Add follow code in one script's OnGUI function

{
	private float originX = 0f;
	private float offsetX = 1.5f;
	void OnGUI()
	{
		if (GUILayout.Button("Create Cube",GUILayout.Height(50))) 
		{
			GameObject objCube = GameObject.CreatePrimitive(PrimitiveType.Cube);
			objCube.AddComponent<Rigidbody>();
			objCube.GetComponent<Renderer>().material.color = Color.blue;
			objCube.transform.position = new Vector3(originX, 0, 0);
			objCube.GetComponent<Rigidbody>().velocity = Vector3.up * 10;

			originX += offsetX;
			
		}
	}
}

Onece we press the button, we'll create an GameObject as Cube and add a RigidBody on it


Let's try something more primitive:create a empty game object.
add the follow code in OnGUI

	void OnGUI()
	{
		if (GUILayout.Button("Create GameObject",GUILayout.Height(70))) 
		{
			GameObject emptyObj = new GameObject("empty gameobject");
			emptyObj.AddComponent<AddedScript>();
		}
	}

now create a new script name it  AddedScript.cs and add follow code in it's Awake() 

	void Awake()
	{
		Debug.Log("beAdded");

	}

when press "Create GameObject" ,we'll create an empty gameObject and add this script, then the console will put out "beAdded".

#Find and Destroy GameObject
Add follow code in GameManager's onGUI() method

		if (GUILayout.Button("Destroy empytObject",GUILayout.Height(90))) 
		{
			var beDesObj = GameObject.Find("empty gameobject");
			if (beDesObj != null) 
			{
				Destroy(beDesObj);
			}

		}

and then add follow code in AddedScript 

	void OnDestroy()
	{
		Debug.Log("be Destroy");
	}

So before that let's press "Create GameObject" several times to create some "empty gameobject",
and then we press the "Destory emptyObject",each time when we press this button,one cloned"empty gameobject" be destoryed 

#Find with tag and Destroy them all
First Let's add "emTag" in inspector
add this statement in "Create GameObject"
			...
			emptyObj.tag = "emTag";
			...
This statement set emptyObj's tag to "emTag" 
Let's change the content in "Destroy emptyObject"

			var emptyObjects = GameObject.FindGameObjectsWithTag("emTag");

			foreach (var obj in emptyObjects) 
			{
				Destroy(obj);
				
			}



