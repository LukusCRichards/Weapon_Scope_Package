# Weapon Scope Documentation

This documentation will mention how you can make it and how to put it together and use it for other GameObjects.

## What Components are Needed for this

These are the components that will be needed to make this package work:

2 Camera Components

1 GameObject with the Weapon you want to be able to zoom with (E.G. A sniper)

1 Script

1 Animator Component (for aiming amination)

## Writing the Script

After opening Unity, create a C# script and name it Scope (or another name you find appropriate). Next open the file, **delete the Start method** and write 8 variables (6 need to be public and 2 need to be private), and write the following:

    public Animator scopeAnimator;

    public float scopeOverleyDelay = .15f;

    public GameObject scopeOverlay;
    public GameObject weaponCamera;
    public Camera mainCamera;

    public float scopedFOV = 15f;
    private float normalFOV;

    private bool isScoped = false;

Now create 2 void methods called **Scoping** and **UnScope**, as well as an **IEnumerator** called **OnScope** and put the Scoping method in the **Update** method.

### Scoping Method

The Scoping method is what will be used to make the aiming animations play back and forth, as well as zooming in and out, by controlling the other two methods that are responsible for this. In the Scoping method write the following:

    void Scoping()
    {
        if (Input.GetButtonDown("Fire2"))
        {
            isScoped = !isScoped;
            scopeAnimator.SetBool("Scoped", isScoped);
            
            if (isScoped)
            {
                StartCoroutine(OnScope());
            }
            else
            {
                UnScope();
            }
        }
    }

The **"Fire2"** in the parentheses is the right mouse button, but you can change this by going to **Edit > Project Settings > Input Settings**.

If you notice at the start of the **GetButtonDown** if statement, there's an interesting bool value: **isScoped = !isScoped;**. The exclamation mark (**!**) is what is used to change the value of it without having to write true or false. In other words, if you use it, you're saying in the code *isScoped equals the opposite of it's current bool value*. If you use it in a if statement such as **if(!isScoped)** you're saying the same thing, but in shorter detail.

In the referenced **scopeAnimator** variable, the isScoped bool is equal to **true**, because the code goes from **top to bottom** and at the very top of the code, the isScoped has a value of **false** and right before the code gets to the scopeAnimator variable, the value of isScoped is changed to **true**.

### OnScope

The Coroutine for the **OnScope** method is what is responsible for the delay before the scope's UI overley is displayed, but the animation is not delayed as it starts imediately before the Coroutine is called in the if statement.

In the OnScope method, the following should be included:

    IEnumerator OnScope()
    {
        yield return new WaitForSeconds(scopeOverlayDelay);

        scopeOverlay.SetActive(true);
        weaponCamera.SetActive(false);

        normalFOV = mainCamera.fieldOfView;
        mainCamera.fieldOfView = scopedFOV;
    }

The **scopeOverlay** GameObject variable is what will contain the image GameObject in the UI Canvas to give it that crosshair look and the **weaponCamera** is what will be used to change the field of view of the camera to make it zoom and turn of the main camera so it only uses the weapon camera. When the scopeOverlay GameObject is set to true, the UI crosshair image will be shown and when it is set to false, it gets turned off.

The private **normalFOV** float variable is what sets the mainCamera's field of vision back to it's original position before you zoomed in and the **scopedFOV** float variable is what sets the zoom distance of the weaponCamera.

The **scopeOverlayDelay** float variable is what will change the delay between when the scopeOverley GameObject becomes visible and should be editable thourgh the inspector as the animations can be different depending on the length of your animations.

Do not worry about the weapons still being visible while zooming in, the 2 camera GameObjects can take care of it on their own.

### Unscope

The UnScope method is what puts everything back to the way everything was before you started zooming in through the scope by turning off the crosshair UI Image and what returns the camera's zoom distance back to it's original position through the normalFOV float vairable.

Make sure the following is included in the UnScope method.

    void UnScope()
    {
        scopeOverlay.SetActive(false);
        weaponCamera.SetActive(true);

        mainCamera.fieldOfView = normalFOV;
    }

Remember that the GameObjects being set to true or false. If it's set to true, it's visible and if it's set to false, it's invisible. As well as the **normalFOV** float variable, as this will move the main camera's field of view back and forth when you press the right mouse button to zoom out.

## Putting Everything Together

### Weapon Holder (GameObject)

If you've written all the code correctly, you shouldn't encounter any errors after doing these next steps.

In the hierarchy, create an empty GameObject and call it **Weapon Holder**, then **import** the **WeaponScope script** component into it as this is what will be used for the weapon animations and the zoom in functionality.

Now parent your sniper to the **Weapon Holder GameObject** and then parent the Weapon Holder GameObject to the **Main Camera**. Make sure the sniper is **centered** to the Weapon Holder's axis by **right clicking** on the sniper's **transform Component** and selecting **reset**, and **only** move the Weapon Holder GameObject to move the sniper to a view point you think is appropriate.

If you decide to move it close to the screen and you see it getting cut off for being too close to the camera, change the **Clipping Planes** value so it no longer looks cut off. The lower the number, the closer the distance for the sniper to get cut off by the camera. The bigger the number, the longer the distance for the sniper to get cut off.

Now parent the Weapon Holder to the Main Camera

### Animation

Select the Weapon Holder GameObject, go to the Animation tab and click on create. When a new window appears, create a new folder in the Assets folder called **Animations** and inside that folder make another folder called **Sniper** to keep things organised. Then name your animation to **Idle** and save it. With the Idle animation selected, click on a frame that's a few seconds off from the first frame with the record button selected and move the **Weapon Holder GameObject** down a bit. Then copy the first frame and move it a few seconds after the Keyframe you just made so it moves back to the exact location.

If you want to smooth the movements a bit, highlight all of the frames, richt-click and select **Flat**. Now you should see the movements of the sniper look smoother. You can check by clickin on the **Curves** tab at the bottom right corner on the left of the frames.

Then click on the Idle animation tab and click on **Create New Clip** and name it as **Scope**. Now all you need to do is move the Weapon Holder GameObject to a position so it looks like the player is looking through the scope using the very first frame of the scope animation. Don't worry about errors in the animation, the transistions will take care of themselves.

If you're changing the position through the Transform component's axis values, you can hold Alt and drag the values so it moves more slowly and allows you to move them closer to the position you desire.

When all this is done, the next thing to do is to go to the scope folder you made in the animations folder and select the Animation controller that has the same name as the GameObject you animated. When it is selected click on the Animator tab and you should see the animations you made. If you don't see the Animator tab to go **Windows > Animator** and it should appear.

In the Animator tab, richt-click on the Idle animation and select **Make Transition** and link it to the **Scope** animation, and do the same thing the other way around.

## Things to note

This script does not comtain any movement aspects, but it should work fine on other game objects that do contain movement in it. Whether it's the camera or the character itself.
