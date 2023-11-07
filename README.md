# Player-Movement-Dash-Tutorial
2 in 1 Tutorial on how to add movement for a 2D platformer game; Dash mechanic as well

1. Create and empty game object called platform and create a square object and name it platform. Make sure all platforms have a Box Collider 2D and a Sprite Renderer on it. You can set up your scene the you want.

   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/d9f7e073-256f-4013-a9a0-0a31d6ec512a)
   
   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/08eafd31-bea9-4f14-b111-29378bfbb0b7)
   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/dbb409d2-b488-43a7-b17d-066fb4914c31)
   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/c04b3a7e-61c5-4cc1-a43e-4800a9b500ad)

This is how I set up my scene for this tutorial:
![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/941810d6-40d6-4b5b-a6bc-26d69672cf94)

2. Create another empty game object this time name it 'Player'. Add a player sprite and link it as a sqaure (for this case i used a circle sprite from unity as my player)

   Make sure to add a Sprite Renderer, Rigidboy 2D and Box Collider 2D to this. ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/5f143be2-d537-417a-a0d2-a2a2264c151d)

   Add a ground check and position it underneather our player sprite.

   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/07ec3792-a15b-4a73-b158-12ccc0be8822)
   
   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/253cf11c-39cf-45f5-b5b5-2640d2d88f23)
   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/eeada807-8932-4811-a5d9-53da0dba6fb1)

3. Add a Trail renderer to our player and change the width, time and colour but this is completely up to you. (this gives our player a trail whne dashing).
   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/c6edb0e4-26b3-4e92-93b5-876395bab3f2)

4. Add a script to our player and name it PlayerMovement

   ![image](https://github.com/MartTQ/Player-Movement-Dash-Tutorial/assets/123513842/f67107e2-298e-4841-8661-6c5059a31729)

   As for the code this is the coding script for the movement + dashing:

using System.Collections;

using System.Collections.Generic;

using UnityEngine;

public class PlayerMovement : MonoBehaviour

{
    private float horizontal;
    private float speed = 8f;
    private float jumpingPower = 16f;
    private bool isFacingRight = true;

    private bool canDash = true;
    private bool isDashing;
    private float dashingPower = 24f;
    private float dashingTime = 0.2f;
    private float dashingCooldown = 1f;

    [SerializeField] private Rigidbody2D rb;
    [SerializeField] private Transform groundCheck;
    [SerializeField] private LayerMask groundLayer;
    [SerializeField] private TrailRenderer tr;

    void Update()
    {
        if (isDashing)
        {
            return;
        }

        horizontal = Input.GetAxisRaw("Horizontal");

        if (Input.GetButtonDown("Jump") && IsGrounded())
        {
            rb.velocity = new Vector2(rb.velocity.x, jumpingPower);
        }

        if (Input.GetButtonUp("Jump") && rb.velocity.y > 0f)
        {
            rb.velocity = new Vector2(rb.velocity.x, rb.velocity.y * 0.5f);
        }

        if (Input.GetKeyDown(KeyCode.LeftShift) && canDash)
        {
            StartCoroutine(Dash());
        }

        Flip();
    }

    private void FixedUpdate()
    {
        if (isDashing)
        {
            return;
        }
        rb.velocity = new Vector2(horizontal * speed, rb.velocity.y);
    }

    private bool IsGrounded()
    {
        return Physics2D.OverlapCircle(groundCheck.position, 0.2f, groundLayer);
    }

    private void Flip()
    {
        if (isFacingRight && horizontal < 0f || !isFacingRight && horizontal > 0f)
        {
            isFacingRight = !isFacingRight;
            Vector3 localScale = transform.localScale;
            localScale.x *= -1f;
            transform.localScale = localScale;
        }
    }
    private IEnumerator Dash()
    {
        canDash = false;
        isDashing = true;
        float originalGravity = rb.gravityScale;
        rb.gravityScale = 0f;
        rb.velocity = new Vector2(transform.localScale.x * dashingPower, 0f);
        tr.emitting = true;
        yield return new WaitForSeconds(dashingTime);
        tr.emitting = false;
        rb.gravityScale = originalGravity;
        isDashing = false;
        yield return new WaitForSeconds(dashingCooldown);
        canDash = true;
    }
}




   


