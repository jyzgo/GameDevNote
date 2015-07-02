##Mouse events
Let's create a cube and add follow code in it's script

	void OnMouseEnter()
	{
		Debug.Log("enter");

	}

	void OnMouseDown()
	{
		Debug.Log("down");
	}

	void OnMouseUp()
	{
		Debug.Log("up");
	}

	void OnMouseExit()
	{
		Debug.Log("exit");
	}

	void OnMouseDrag()
	{
		Debug.Log("drag");
	}

	void OnMouseOver()
	{
		Debug.Log("over");
	}

All these method has to be the implement of MonoBehaviour

##Drag the cube

Let's change the content of OnMousceDrag() 

	IEnumerator  OnMouseDrag()
	{
		 	var screenSpace = Camera.main.WorldToScreenPoint(transform.position);
		    //transform mouse position to world pos and calculate the distance between it and the object
		    var offset = transform.position - Camera.main.ScreenToWorldPoint(new Vector3(Input.mousePosition.x, Input.mousePosition.y, screenSpace.z));

		    while (Input.GetMouseButton(0))
		    {

		        var curScreenSpace = new Vector3(Input.mousePosition.x, Input.mousePosition.y, screenSpace.z);
		        var curPosition = Camera.main.ScreenToWorldPoint(curScreenSpace) + offset;
		        transform.position = curPosition;
		        yield return null;
		    }

	}


##Instanite and Destory

Modify code like follow

	public GameObject initObj;

	private List<GameObject> saveList;

	void Awake()
	{
		saveList = new List<GameObject>();
	}

	void OnGUI()
	{
		if (GUILayout.Button("clear list",GUILayout.Height(20)) )
		{
			foreach (var obj in saveList) 
			{
				Destroy(obj);
				
			}
			
		}

	}
	void OnMouseDown()
	{
		// Debug.Log("down");
		GameObject curObj = Instantiate(initObj);
		var cyliderSc = curObj.GetComponent<CyliderTest>();
		
		saveList.Add(curObj);
	}


Once we touch the cube, a initObj will be cloned and add to the List.
And we click the "clear list" button, all of them will be destoried;

#Singleton Test

Create a script name "NoBehave.cs",Don't attach it to any object;
and add a public value to it

	public static string testStr = "testStr";

Now back to the TouchScript
add follow code 
	void OnMouseDown()
	{
		Debug.Log("no be" + NoBehave.testStr );
	}

Now when we mouse down the cube, there'll be a output.

Let's delete the ": MonoBehaviour" from "NoBehave.cs",make it as a common class inherited nothing. 
And there's output too;

Now let's build a singleton as follow

public class NoBehave  {

	private static NoBehave _instance = null;
	public static NoBehave getInstance()
	{
		if (_instance == null) 
		{
			_instance = new NoBehave();
			
		}

		return _instance;
	}

	public int testNum = 10;

	public void setTestNum(int a)
	{
		testNum = a;
	} 

}

and change the content of TouchScript like this 

	void OnMouseDown()
	{
		int num = NoBehave.getInstance().testNum;
		Debug.Log("num" + num);
		NoBehave.getInstance().setTestNum(num + 1);
	}

Now when we touch the cube, we'll get the log

And now ,let's copy the cube prefab, and presee them alternatly,
we'll find the num is always raise up.
