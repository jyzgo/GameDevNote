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
