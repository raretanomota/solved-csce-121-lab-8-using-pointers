Download Link: https://assignmentchef.com/product/solved-csce-121-lab-8-using-pointers
<br>
The purpose of this lab is to practice using pointers.

The main work is in writing the check_all_guests_symmetric and last_man_seated functions, all the preceding is to help by giving you some scaffolding functions.

Knights partying like its 999

Types

In this lab we’re interested in the knights of King Arthur’s court who’ve come to Camelot as part of a New Year’s celebration. We need to represent data to describe the knights; we do this with the following types:

#define STRLEN 200 struct party_goer_t; // Declare the type for a party-goer, to be defined later typedef struct party_goer_t * party_goer_p; // A pointer to a party-goer struct party_goer_t { // Now we define the type    char name[STRLEN]; // The name of the person    party_goer_p came_with; // Person who came with this person; NULL if alone.    party_goer_p sitting_to_left; // Person seated to his/her left.};

The idea is that each person at the party will be represented by an instance of this structure. It has data to represent the attendee’s details and also two pointers (of type party_goer_p) that refer to other attendees. This is how it is intended to work:

<ul>

 <li>We use the name field to store the attendee’s name.</li>

 <li>People can arrive singly or together as part of a pair. If a person arrives together with someone else, then the came_with pointer is set up to point to the other person. If the person came alone, the pointer should be NULL.</li>

 <li>Once people arrive, they are seated at a big table. Their seating arrangement is represented via the sitting_to_left pointer. For attendee guest the reference guest.sitting_to_left refers to the person seated to guest’s left.</li>

</ul>

<h3>Printing and sanity checking</h3>

Now we want to use these structures to describe the situation on the 31st of December. Below is a snippet of code that initializes elements in a way that reflects knights seated together at the famous round table, as illustrated in the figure.

<table>

 <tbody>

  <tr>

   <td width="72%">party_goer_t kay = {“Kay”, NULL, NULL};party_goer_t bors = {“Bors the Younger”, NULL, &amp;kay};party_goer_t lamorak = {“Lamorak”, NULL, &amp;bors};party_goer_t gawain = {“Gawain”, NULL, &amp;lamorak};party_goer_t percival = {“Percival”, NULL, &amp;gawain};party_goer_t geraint = {“Geraint”, NULL, &amp;percival};party_goer_t lancelot = {“Lancelot”, NULL, &amp;geraint};party_goer_t arthur = {“Arthur”, NULL, &amp;lancelot};kay.sitting_to_left = &amp;arthur;</td>

   <td></td>

  </tr>

 </tbody>

</table>

Here is a function that will return true if your pass it a reference to a party_goer_t and they are indeed seated at a round table.

bool at_a_round_table(party_goer_p p){       assert(p != NULL);    party_goer_p next = p;     do {           if (next == NULL)            return false;        next = next-&gt;sitting_to_left;    } while (next != p);     return true;}

Test this claim by passing in a reference to one of the 8 individuals initialized above. Trace through it to observe how it works; notice that it doesn’t matter if you start from &amp;arthur or &amp;percival, or any of the other knights. Unlike an array, there is no privileged start for this data structure. (If you’re following along with the Arthurian connection, remember that was actually the point of Camelot having a round table!)

The next step is to write two functions, one to print the name of an individual, and another that prints the details of whole table given a reference to one person (and assuming that it is a round table).

<table>

 <tbody>

  <tr>

   <td>

    <table>

     <tbody>

      <tr>

       <td><strong>?</strong></td>

       <td>HINT</td>

      </tr>

     </tbody>

    </table></td>

   <td>

    <table>

     <tbody>

      <tr>

       <td>Declare them like this:void print_person(party_goer_p person)void print_round_table(party_goer_p person)</td>

      </tr>

     </tbody>

    </table></td>

  </tr>

 </tbody>

</table>

The next four lines of C++ code will go further and establish that four of the attendees arrived together. The relationship is shown in the figure with little arrows showing the references.

<table>

 <tbody>

  <tr>

   <td width="84%">lancelot.came_with = &amp;arthur;arthur.came_with = &amp;lancelot;geraint.came_with = &amp;lamorak;lamorak.came_with = &amp;geraint;</td>

   <td></td>

  </tr>

 </tbody>

</table>

A basic maxim in computing is <em>“garbage in, garbage out”</em>. This pithy little aphorism sums up the observation that even perfectly correct code may produce bad output if the provided input is malformed. As part of the defensive programming technique we’ve been honing together in class, we should check that the input makes sense. This is important because with pointers there are many more things that can go wrong.

Complete the following function so that, given party-goers seated around a round table, it checks if all came_with relationships are indeed symmetric.

bool check_all_guests_symmetric(party_goer_p p){       assert(p != NULL);    … }

Once you’ve written your code, you can test it on the cases defined here: <a href="http://robotics.cs.tamu.edu/dshell/cs121/l8/t0.cpp">0</a>, <a href="http://robotics.cs.tamu.edu/dshell/cs121/l8/t1.cpp">1</a>, <a href="http://robotics.cs.tamu.edu/dshell/cs121/l8/t2.cpp">2</a>, <a href="http://robotics.cs.tamu.edu/dshell/cs121/l8/t3.cpp">3</a>. These are just short snippets of C++ code intended for you to copy and paste into your program. (It is important that you make sure you understand why you can call check_all_guests_symmetric(test0) like that without needing an index.) Also, <a href="http://robotics.cs.tamu.edu/dshell/cs121/l8/a.cpp">here</a> is an equivalent definition of the original scenario we had above.

<h3>Party game</h3>

<a href="https://www.youtube.com/watch?v=BT_NSsM2brM">A</a>fter much making merry, a fine feast, and rousing chorus fuelled by ample flagons of mead, the bill is to be settled. King Arthur proposes the following game to determine whose purse must provide the coin.

Starting from Arthur and going to his left, they each count down from three. The person who should count <em>“zero”</em> excuses himself from the table. Then, the person who previously said <em>“one”</em> begins again from three. This is repeated until only one person remains. They lose and the bill is theirs to pay. The following figure shows the first two rounds of the procedure.

The knights are suspicious that something may be amiss; maybe Arthur, that skinflint!, did some careful calculation beforehand. They agree to the system but with a crucial modification: before they begin, Arthur must first roll a fair die to determine what the count should start at. Whatever number was rolled replaces the three in the procedure described above.

Write a function to determine who must foot the bill. It should be defined like this, where starter should be Arthur and n the result of the die roll.

party_goer_p last_man_seated(party_goer_p starter, int n){       … }

To answer this question, you should:

<ol>

 <li>The party-goer who is “out” should be removed by updating the appropriate sitting_to_left pointer(s).</li>

 <li>Repeat this until only one knight remains. Again this should be determined solely via sitting_to_left.</li>

</ol>

<em>Important:</em> Don’t use any arrays in your function—that will sideline much of the exercise. A very good solution would work without counting the total number of people, so that even if there were more people at the table than an int or a long could store, it would be correct. (That’s a big bill!)

<h3>The round table revisited</h3>

The statement above about the result computed by the at_a_round_table() function was very carefully worded. That is because it can’t serve as a function to determine whether any seating arrangement is at a round table or not. Can you see why?

<table>

 <tbody>

  <tr>

   <td>

    <table>

     <tbody>

      <tr>

       <td><strong>?</strong></td>

       <td>HINT</td>

      </tr>

     </tbody>

    </table></td>

   <td>

    <table>

     <tbody>

      <tr>

       <td>There are inputs where the function will loop forever. Construct one!</td>

      </tr>

     </tbody>

    </table></td>

  </tr>

 </tbody>

</table>

What is the challenge in writing an at_a_round_table() function that does?

<table>

 <tbody>

  <tr>

   <td>

    <table>

     <tbody>

      <tr>

       <td><strong>?</strong></td>

       <td>HINT</td>

      </tr>

     </tbody>

    </table></td>

   <td>

    <table>

     <tbody>

      <tr>

       <td>You have to keep track of whether you’re revisiting an element you’ve already seen. That requires storing some information, but we don’t know how much to store beforehand. Worse still, for any bounded amount chosen beforehand, there are seating arrangements which need more in order to compute the correct result.</td>

      </tr>

     </tbody>

    </table></td>

  </tr>

 </tbody>

</table>