# Boot Camp Live Projects


## Introduction

At The Tech Academy, for the last two weeks in every language program we work in teams on a shared project. In the C# program, that app was an ASP.NET MVC site for a local theater group. It being Covid-time, we worked remotely. We used Slack, Google Meet, and daily scrum meetings to maintain group cohesion. I was able to work on several front end and back end stories.

### Delete users

This story task was to add the ability to delete users from the admin page, while preserving any sorts or filters used on the page. It was a fairly straightforward task with the main hurdle being familiarizing myself with the existing user management system and the database. The implementation on the front end was just a delete button and a confirmation modal.

```
/// <summary>
/// Delete a User from the Admin/UserList
/// </summary>
/// <param name="id">User Id</param>
/// <returns>The referring View path</returns>
[HttpPost, ActionName("DeleteUser")]
[ValidateAntiForgeryToken]
public async Task<ActionResult> UserList(string id)
{
	var userManager = new UserManager<ApplicationUser>(new UserStore<ApplicationUser>(db));

	if (ModelState.IsValid)
	{
		//make sure we got an id
		if (id == null)
		{
			return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		}

		var userToBeDeleted = await userManager.FindByIdAsync(id);
		var userRolesToBeDeleted = await userManager.GetRolesAsync(id);
		//open a connection to the database context, start new transaction
		using (var transaction = db.Database.BeginTransaction()) 
		{
			if (userToBeDeleted.Role == "Subscriber") //if user is a subscriber, remove that data, otherwise fk errors
			{
				var subdata = db.Subscribers.Where(x => id.Equals(id)); //get all the rows with ids that match ours from dbo.Subscribers
				db.Subscribers.RemoveRange(subdata); //delete them
			}
			if (userRolesToBeDeleted.Count() > 0) //if user has roles, get rid of them
			{
				foreach (var item in userRolesToBeDeleted.ToList())
				{
					await userManager.RemoveFromRoleAsync(userToBeDeleted.Id, item);
				}
			}
			await userManager.DeleteAsync(userToBeDeleted); //delete the user
			transaction.Commit(); //commit and close down the connection
		}
		return Redirect(Request.UrlReferrer.PathAndQuery); //if it all worked out, go back to admin/userlist and keep the same sort/filters/search
	}
	else
	{
		return new HttpStatusCodeResult(HttpStatusCode.BadRequest); //something went wrong, invalid model
	}
}
```
