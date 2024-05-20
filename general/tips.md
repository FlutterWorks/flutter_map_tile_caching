# Tips

{% hint style="info" %}
This page is a work in progress! Check back later for more tips and tricks.
{% endhint %}

***

{% hint style="success" %}
**Keep the number of stores small to improve performance!**

FMTC isn't internally optimized to handle large numbers of stores within the database queries and processing, and algorithms often have polynomial time complexities based on the number of stores. This is because of the feature that tiles can belong to multiple stores, and therefore a lot of extra work is required to keep track of this all.
{% endhint %}
