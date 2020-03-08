# Wase Challenge - JWT 1
**Event:** CyberSpike

**Subtitle:** Gain admin access to an authentication API

**Difficulty:** Beginner

**Categories:** JWT, base64

## Description
In this lab you are going to take a look at an API that uses an insecure JSON Web Token (JWT) library to verify tokens. The challenge is to create a valid token without having access to any of the keys used for signing.

Good luck!

## Environment
When you first start up the challenge you are greeted with a web page which has two features: Generating a JWT and validating a JWT.
[PLACEHOLDER API screenshot]

The obvious first step is to explore the UI that you're given. Clicking the **Get new token** button fills the Token field with a freshly generated JWT.
Clicking **Verify token** then returns the payload of the JWT with a message that confirms the validity of the JWT.
[PLACEHOLDER validation screenshot]

## Solution
### Understanding JWT
To solve this challenge we first need to understand how JWT works. Let's take a look at what a JWT looks like.

JWT exists of 3 parts, where each part is a base64 encoded string, seperated with a period.

The first part is the header. This contains information about the JWT, which contains the type of the JWT (Which usually is "JWT"), and the algorithm used to generate the signature (part 3)

The second part is the payload. This contains the actual data.

The third part is the signature. This verifies the intergrity of the JWT. The signature is created with a combination of the algorithm specified in the header of the JWT and a secret key.

Now that we figured out how JWT works, let's break it!

### Solving the challenge
Example JWT: ```eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJKV1QgQ2hhbGxlbmdlIiwiaWF0IjoxNTgzNDIxNzAxLCJyb2xlIjoidXNlciJ9.mYICoVMKnOz9ZMgzvzGPebXfwn3JZCWZ2XfEOcsja-wcAtKRNz5EgyDLEpC3_cyOVk_nSZFzYsCUI0itTL0GFVKxC1Mc9LxQXO6i64RincRYebH-I8FXTSZ8djVyqqSWaf89HShPbOIW2yOerA9AtjjC6DOBR5wpZmFGoyLfg04```

Decoded JWT:

Header: ```{"alg": "RS256", "typ": "JWT"}```

Payload: ```{"iss": "JWT Challenge", "iat": 1583421701, "role": "user"}```

The challenge description states the API uses an insecure JWT library to verify tokens. Using [Jwt.io](http://jwt.io) we can decode the generated JWT to read its payload. We can see that the payload contains a **role** value, which is `user`. We can't simply change the value, as the signature wouldn't match the JWT anymore. However, we can also change the algorithm value in the header to `none`. This will change the JWT to not use a signature. We can use [CyberChef](https://gchq.github.io/CyberChef/#recipe=JWT_Sign('','None')&input=eyJpc3MiOiJKV1QgQ2hhbGxlbmdlIiwiaWF0IjoxNTgzNDIxNzAxLCJyb2xlIjoiYWRtaW4ifQ) to easily sign a new JWT with the values that we need. Our new JWT looks like this: ```eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJpc3MiOiJKV1QgQ2hhbGxlbmdlIiwiaWF0IjoxNTgzNDIxNzAxLCJyb2xlIjoiYWRtaW4ifQ.```
Note that this JWT exists of only 2 parts, the header and the payload. There is no signature, as we used the ```none``` algorithm to sign the JWT.

When we validate this custom signed JWT with the insecure API we can see that our JWT is validated successfully, and we have completed the challenge

# Conclusion
The insecure API verifies the JWT based on the algorithm specified in the header. We can set the algorithm value to `none` to make the JWT not make use of the signature, and change the role to `admin` to gain admin rights on the API. 
