# Weapon Scope Documentation

This documentation will mention how you can make it and how to put it together and use it for other GameObjects.

## What Components are Needed for this

These are the components that will be needed to make this package work:

2 Camera Components

1 GameObject with the Weapon you want to be able to zoom with (E.G. A sniper)

1 UI Canvas with a GameObject containing an Image component

2 Scripts

1 Animator Component (for aiming amination)

## Writing the Script

### Weapon Scope

After opening Unity, create a C# script and name it Weapon Scope (or another name you find appropriate). Next open the file, **delete the Start method** and write 8 variables (6 need to be public and 2 need to be private), and write the following:

    public float scopeOverleyDelay = .15f;

    public GameObject scopeOverlay;
    public GameObject weaponCamera;
    public Camera mainCamera;

    public float scopedFOV = 15f;
    private float normalFOV;

    private bool isScoped = false;

Now create 2 void methods called **Scoping** and **UnScope**, as well as an **IEnumerator** called **OnScope** and put the Scoping method in the **Update** method.

#### Scoping Method

The Scoping method is what will be used to make the aiming animations play back and forth, as well as zooming in and out, by controlling the other two methods that are responsible for this. Now create a private script reference to the **AnimationController script** variable and on top of the Update method, create an Awake method and write the following which should contain these new variables when you're finished:

    AnimationController animator;
    
    void Awake()
    {
        animator = GetComponent<AnimationController>();
    }
    
This is how the animation will be capable of **calling** the **PlayAnimation** function from the AnimationController script (AnimationController is further down).

In the Scoping method write the following:

    void Scoping()
    {
        if (Input.GetButtonDown("Fire2"))
        {
            isScoped = !isScoped;
            animator.PlayAnimation();
            
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

#### OnScope

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

#### Unscope

The UnScope method is what puts everything back to the way everything was before you started zooming in through the scope by turning off the crosshair UI Image and what returns the camera's zoom distance back to it's original position through the normalFOV float vairable.

Make sure the following is included in the UnScope method.

    void UnScope()
    {
        scopeOverlay.SetActive(false);
        weaponCamera.SetActive(true);

        mainCamera.fieldOfView = normalFOV;
    }

Remember that the GameObjects being set to true or false. If it's set to true, it's visible and if it's set to false, it's invisible. As well as the **normalFOV** float variable, as this will move the main camera's field of view back and forth when you press the right mouse button to zoom out.

### Animation Controller

Now crearte another scipr and name it Animation Controller and write a **public Animator** vairable and a **private script reference** variable to the **WeaponScope** script. By now, what you've written should look like this: 

    public Animator scopeAnimator;
    
    WeaponScope scope;

**This script controls the animations of the weapon**.

Now **delete** the **Start** and **Update** functions and create an **Awake** method, then write the relevant so it looks like this:

    void Awake()
    {
        scope = GetComponent<WeaponScope>();
    }

#### PlayAnimation

Below the Awake function create a **public** function called PlayAnimation underneath the Awake method and write the following:

    public void PlayAnimation()
    {
        scopeAnimator.SetBool("Scoped", scope.isScoped);
    }

If you're written these correctly, your animation should play without any issues when the other bits are finished.

## Readying the Other Assets for the Script

This is what you need to do to make sure the script has something to work with.

### Weapon Holder (GameObject)

If you've written all the code correctly, you shouldn't encounter any errors after doing these next steps.

In the hierarchy, create an empty GameObject and call it **Weapon Holder**, then **import** the **WeaponScope script** component into it as this is what will be used for the weapon animations and the zoom in functionality.

Now parent your sniper to the **Weapon Holder GameObject** and then parent the Weapon Holder GameObject to the **Main Camera**. Make sure the sniper is **centered** to the Weapon Holder's axis by **right clicking** on the sniper's **transform Component** and selecting **reset**, and **only** move the Weapon Holder GameObject to move the sniper to a view point you think is appropriate.

If you decide to move it close to the screen and you see it getting cut off for being too close to the camera, change the **Clipping Planes** value so it no longer looks cut off. The lower the number, the closer the distance for the sniper to get cut off by the camera. The bigger the number, the longer the distance for the sniper to get cut off.

Now parent the Weapon Holder to the Main Camera and whenever the character moves, the sniper will remain in view.

### Animation

Select the Weapon Holder GameObject, go to the Animation tab and click on create. When a new window appears, create a new folder in the Assets folder called **Animations** and inside that folder make another folder called **Sniper** to keep things organised. Then name your animation to **Idle** and save it. With the Idle animation selected, click on a frame that's a few seconds off from the first frame with the record button selected and move the **Weapon Holder GameObject** down a bit. Then copy the first frame and move it a few seconds after the Keyframe you just made so it moves back to the exact location.

If you want to smooth the movements a bit, highlight all of the frames, richt-click and select **Flat**. Now you should see the movements of the sniper look smoother. You can check by clickin on the **Curves** tab at the bottom right corner on the left of the frames.

Then click on the Idle animation tab and click on **Create New Clip** and name it as **Scope**. Now all you need to do is move the Weapon Holder GameObject to a position so it looks like the player is looking through the scope using the very first frame of the scope animation. Don't worry about errors in the animation, the transistions will take care of themselves.

If you're changing the position through the Transform component's axis values, you can hold Alt and drag the values so it moves more slowly and allows you to move them closer to the position you desire.

When all this is done, the next thing to do is to go to the scope folder you made in the animations folder and select the Animation controller that has the same name as the GameObject you animated. When it is selected click on the Animator tab and you should see the animations you made. If you don't see the Animator tab to go **Windows > Animator** and it should appear.

In the Animator tab, richt-click on the Idle animation and select **Make Transition** and link it to the **Scope** animation, and do the same thing the other way around. Now select **Parameters** and set a bool parameter called **Scoped**, then click on the arrow from the Idle animation to the Scope animation to create a condition and set the Scoped bool to **true**. Do the same thing for the Scope, but set the Scoped bool to **false**.

There will be a problem with this when you change the bool while the game is playing because when the Scoped bool is set to true, **the transition only starts when the Idle animation has finished playing**. This is what is called **Exit Time**. To turn this of, select the transition arrows between the animations and untick Exit Time. Now the animation should start playing as soon as the Scoped bool is set to true.

If you want to change the speed of the transitions, select the transition arrows, go to settings and change the transition duration values. The lower the number, the quicker the transition and the bigger the number, the longer the transition.

### Sniper Crosshair

In the hiererchy, right-click, go to **UI**, select **Image** and now a new Canvas should appear with a GameObject called Image. The Image GameObject is what you will use for the sniper crosshairs.

If you don't have a sniper crosshairs image, you can quickly make one by going to Photopea. Make sure that the area that you want to be visible through the screen while scoping is transparent. If you do not, you won't be able to see anything when zoomed in. After you've made it, export the image as a PNG as that format is what supports transparent images.

Then create a folder called **Images** and create another inside it called **Sniper** to organise it. Now import the crosshair Image into that folder and select it and change the **Texture Type** to **Sprite (2D & UI)** so it can be used in the Image components. Then go to the Image GameObject in the Canvas and change the source image to the crosshair image. Now the white box Image that used to be there should now look like the crosshair.

If the Image looks out of shape, go to the Canvas and change the **UI Scale Mode** to **Scale With Screen Size** and set the **Match** value from Height (0) to Height (1) so everytime the window gets taller, it scales with it. To make it easier to know where the sniper crosshair is, change the Canvas image to **Scope Overley**.

Make sure that the Scope Overlay GameObject is turned off so it is not visible while you continue to do the other bits.

### Weapon Camera

In the hierarchy, select the Main Camera GameObject and create a new Camera so it is parented to the normal camera and follows everything the Main Camera does. Make sure you reset the transform so there is not offset and create a new layer and call it **Weapons**. Then select the Weapon Holder GameObject and change the layer to Weapon and make sure it changes the layer of its children as well.

Now change the name of the new Camera you created to **Weapon Camera**, select **Culling Mask** and make sure that the only layer it is set to is the Weapons layer and change **Clear Flags** to **Depth Only**, set the **Depth** to 1 so it is set above Main Camera and change the Near value of the Clipping Plane to 0.1. In the Main Camera, go to the Culling Mask and turn off the Weapons layer so it no longer shows anything containing the Weapons layer and overlays the Weapon Camera with the Main Camera.

Now whenever you get close to an obstacle, it will not longer clip through it because the main camera does not render the GameObjects with the Weapons layer and the Weapon Camera is only rendering the GameObjects with the Weapons layer.

## Combining Everything

Once you've done everything, all you need to do is to put everything to its corresponding slots. The Weapon Camera and Main Camera goes in their corresponding slots, along with the Scope Overlay GameObject. The Weapon Holder Animator Controller goes into the Scope Animator slot in the Animation Controller script Component.

If you've done everything correctly, you should not encounter any problems. If you feel like the scope Overlay is taking too long to appear or appearing too quickly, simply change the value of the Scope Overlay Delay.

## Things to note

This script does not comtain any movement aspects, but it should work fine on other game objects that do contain movement in it. Whether it's the camera or the character itself.

If any GameObject other than the Weapons Holder is containg the Weapons layer, they too will not be rendered when zoomed in. To fix this, simply change their layers. 

**IMPORTANT:** Referencing the other scripts from the awake functions are **paramount**, because if they are not included, they will not be able to work together.
