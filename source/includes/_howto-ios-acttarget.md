## Handling Act Targets - IOS

### 1. Identify the Act payload

```swift
let act = payload.act?.act      // Information for linked Act
let product = payload.product   // Information for linked Product
let category = payload.category // Information for linked Category

// Any of the above are not `null`, and a `customUrl` is detected, we need to redirect the user to this linked entity
let actCustomUrl = act?.customUrl
let productCustomUrl = product?.customUrl
let categoryCustomUrl = category?.customUrl
```
Acts and Information Pages are closely related. An information page simply displays the merchant's message to the consumer.  An Act is like a info page that includes a form the user must fill out. 

If `payload.act?.act` is not null, and `act?.customUrl` IS null, you have an Act. 

### 2. Display the Act

```swift
let actId = act?.id
let actPageBuildingBlocks = act?.pageBuildingBlocks
let actIsInformationPage = act?.isInformationPage
let actQuestions = act?.questions

// Iterate through the Act Page Building Blocks and render on screen
for actPageBuildingBlock in actPageBuildingBlocks! {
    print(actPageBuildingBlock)
}

// Iterate through the Act Questions and render on screen
for actQuestion in actQuestions! {
    print(actQuestion)
}
```
A merchant creates an Act by adding display items (building blocks) and then adding form questions.  

Iterate first through the building blocks, and then through the questions, rendering them on screen. 

### 3. Submitting Act answers 

```swift
let user: User // Current user
let location: CLLocation? // Current user location if available
let sspAct: SspAct // SSP Act to submit 
let page: Page // User answers

func answer(element: Page.Element) -> SspSubmissionAnswer? {
	switch element {
	case .text, .divider, .image, .video:
		return nil
	case .dateField(let dateField):
		guard let value = dateField.value else {
			return nil
		}
		let date = // Map Date to string
		return SspSubmissionAnswer(questionId: dateField.id, answer: date)
	case .select(let select):
		guard let value = select.value else {
			return nil
		}
		return SspSubmissionAnswer(questionId: select.id, answer: String(value.value))
	case .textField(let text):
		guard let value = text.value else {
			return nil
		}
		return SspSubmissionAnswer(questionId: text.id, answer: value)
	}
}

// Map page inputs to submission answers
let answers = page.elements.compactMap(answer(element:))
```

The example to the right shows an easy way to map all user provided answers into a meaningful object.


### 3a. How to format the Act submission

```swift
// Creating Ssp Act Sumbission model
let submission = SspActSubmission(
	userId: user.id,
	userName: user.username,
	personTitle: user.title,
	firstName: user.firstName,
	lastName: user.lastName,
	email: user.email,
	phone: user.phone,
	scanId: sspAct.scanId,
	latitude: location?.latitude,
	longitude: location?.longitude,
	answers: answers
)
```

Next you will need to create the basic model for submitting an Act. All of the previously declared details must be preset in the structure, like User, Act, Location, Answers.


### 3b. Do the submission

```swift
// Submission
sspActManager.submitAct(actId: sspAct.id, actSubmission: submission) { (result) in
	switch result {
	case .success(let submission):
		// Act submitted
	case .failure(let error):
		// Handle error gracefully
	}
}

```

Lastly, execute the final form of the object passed on one of the method’s parameters.

## Look Up An Act By Id - IOS

```swift
rezolveSession?.sspActManager.getAct(actId: id) { (result) in
	switch result {
	case .success(let act):
		if let pageBlocks = act.pageBuildingBlocks {
			// pageBuildingBlocks can be used directly to render UI, or PageBuilder can be used to map blocks to more useful models (user input, validation)
			do {
				let page = try PageBuilder().build(from: pageBlocks)
				// Use page model to render UI
			} catch {
				// Handle error gracefully
			}

		} else {

		}
		let isInformationPage = act.isInformationPage
		// submission should not be available if isInformationPage == true
	case .failure(let error):
		// Handle error gracefully
	}
}

```

If you have an Act Id, you can retrieve it using the example code to the right. 