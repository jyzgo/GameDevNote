####Input 

<pre><code>
	void FixedUpdate ()
	{
		float h = Input.GetAxisRaw ("Horizontal"); //+1 -1 0;
		float v = Input.GetAxisRaw("Vertical");
		
		float hf = Input.GetAxis ("Horizontal"); //any float value between (-1,1);
		float vf = Input.GetAxis("Vertical");
	}

</code>></pre>


####Ray cast

<pre><code>

	int floorMask;

	void Awake ()
	{
		floorMask = LayerMask.GetMask("Floor");
	}
	
	void FixedUpdate ()
	{
		Turning ();

	}
	
	void Turning ()
	{
		Ray camRay = Camera.main.ScreenPointToRay (Input.mousePosition);

		RaycastHit floorHit;

		if (Physics.Raycast (camRay, out floorHit, camRayLength, floorMask)) 
		{
			Vector3 playerToMouse = floorHit.point - transform.position;
			playerToMouse.y = 0;

			Quaternion newRotation = Quaternion.LookRotation(playerToMouse);
			playerRigidbody.MoveRotation(newRotation);
		
		}

	}
</code></pre>