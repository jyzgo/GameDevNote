######Script Study

##Lift cycle

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

"Awake 1"
"Start 1"
"FixedUpdate 1"
"update 1"
"Late update 1"
"On GUI 1"

So according to the log, we have already know the sequence of the invocation
But the FixedUpdate here is called base on interval,so this the different.

##Mutiple Script invocate sequence

The same object,let's add another script, but set it's scriptIndex as 2

"Awake 2"
"Awake 1"
"Start 2"
"Start 1"
"FixedUpdate 2"
"FixedUpdate 1"
"update 2"
"update 1"
"Late update 2"
"Late update 1"
"On GUI 2"
"On GUI 1"

add again as 3

"Awake 3"
"Awake 2"
"Awake 1"
"Start 3"
"Start 2"
"Start 1"
"FixedUpdate 3"
"FixedUpdate 2"
"FixedUpdate 1"
"update 3"
"update 2"
"update 1"
"Late update 3"
"Late update 2"
"Late update 1"
"On GUI 3"
"On GUI 2"
"On GUI 1"

As we see, the latest script called the first
and when we stop the game, we'll finde the OnDestroy() be called

"Destory1"
"Destory2"
"Destory3"

The oreder of the calls is reversed
