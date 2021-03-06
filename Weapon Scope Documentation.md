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

In the Ons=Scope method, the following should be included:

    IEnumerator OnScope()
    {
        yield return new WaitForSeconds(scopeOverleyDelay);

        scopeOverlay.SetActive(true);
        weaponCamera.SetActive(false);

        normalFOV = mainCamera.fieldOfView;
        mainCamera.fieldOfView = scopedFOV;
    }



### Unscope

The UnScope method is what puts everything back to the way everything was before you started zooming in through the scope.

## Putting Everything Together


## Things to note

This script does not comtain any movement aspects, but it should work fine on other game objects that do contain movement in it. Whether it's the camera or the character itself.
