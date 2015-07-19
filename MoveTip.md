###Move Tip

<pre><code>
	
	void FixedUpdate ()
	{
		float h = Input.GetAxisRaw ("Horizontal");
		float v = Input.GetAxisRaw("Vertical");
		Move (h, v);
	}
	void Move (float h, float v)
	{
		//movement is a Vector3
		movement.Set (h, 0f, v);

		//get the direction and mutiply speed and deltaTime
		movement = movement.normalized * speed * Time.deltaTime;

		//playerRigidbody is a rigidbody
		//***** if the gameobject has a rigidbody,we shoule use rigidbody to set postion see follow doc
		//***** http://docs.unity3d.com/ScriptReference/Rigidbody-position.html
		playerRigidbody.MovePosition (transform.position + movement);


	}


</code></pre>


###Camera follow

<pre><code>
	
	public Transform target;
	public float smoothing = 5f;

	Vector3 offset;

	void Start()
	{
		//set the original offset
		offset = transform.position - target.position;
	}


	void FixedUpdate()
	{
		Vector3 targetCamPos = target.position + offset;
		//smoothly move to the target position, and the key word is lerp 
		transform.position = Vector3.Lerp (transform.position, targetCamPos,smoothing * Time.deltaTime);


	}
</code></pre>