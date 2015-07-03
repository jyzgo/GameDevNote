#Create an cube
Let's create a cube from empty GameObject
Create empty GameObject
add mesh filter ->select mesh ->cube
add mesh render
add a default meterial


#Destroy Test

Create a new GameObject and add follow code to it's new script 
	public GameObject resoucePrefab;
	// Use this for initialization
	private List<GameObject> testList;
	void Start () {
		testList = new List<GameObject>();
		for (int i = 0; i <  10; i ++) 
		{
			var curObj = Instantiate(resoucePrefab);
			testList.Add(curObj);
		}

		for (int i = 0; i <  testList.Count; ++i) 
		{
			Destroy(testList[i]);
		}

		for (int i = 0; i <  testList.Count; ++i) 
		{
			Destroy(testList[i]); //!!!!Destroy twice!!
		}


		Debug.Log("count " + testList.Count);
		testList[0].transform.position = new Vector3(0f, 10f, 0f);
		//testList[0] shouldn't exist now.
		Destroy(testList[0],2f);
		testList[0].AddComponent<Rigidbody>();
		testList[0].GetComponent<Rigidbody>().velocity = new Vector3(0f, 1f, 2f);
		testList[0].AddComponent<OutScript>();
	
	}

Let's create a script named OutScrit.cs
And add follow code:
public class OutScript : MonoBehaviour {

	// Use this for initialization
	void Start () {
		Debug.Log("Be added");
	
	}
	void OnDestroy()
	{
		Debug.Log("OutScript Destroy");
	}
}
As we see, the testList Destroy it's GameObjects twice! which is quite dangerous!
If we do this in C++, it'll cause a inevitable crash.
But it didn't happen.
And we find that, the "Be added" still output in console,but the OnDestroy() be excuted immediately.

