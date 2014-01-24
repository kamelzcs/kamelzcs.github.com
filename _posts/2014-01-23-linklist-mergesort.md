---
layout: post
title: Linklist Mergesort
date: 2014-01-23 18:27:36
isOriginal: true
category: Data structure
tags:
 - Datastructure
 - Sourcecode
keywords: Data structure, Source Code
description: 
---

#### Linkedlist mergesort in Linux kernal

Merge sort on array can be easily done due to it is easy to find the specific data in 
a any given position index value. While, if it were the case in the linkedlist, it would
be tough to implemente the mergesort efficienty. 

Thanks to the [post](http://cstheory.stackexchange.com/questions/19759/core-algorithms-deployed/19773#19773), 
I found the following source code in the [Linux 2.6](https://github.com/mirrors/linux-2.6/blob/master/lib/list_sort.c)
{%highlight c%}
/*
 * Returns a list organized in an intermediate format suited
 * to chaining of merge() calls: null-terminated, no reserved or
 * sentinel head node, "prev" links not maintained.
 */
static struct list_head *merge(void *priv,
				int (*cmp)(void *priv, struct list_head *a,
					struct list_head *b),
				struct list_head *a, struct list_head *b)
{
	struct list_head head, *tail = &head;

	while (a && b) {
		/* if equal, take 'a' -- important for sort stability */
		if ((*cmp)(priv, a, b) <= 0) {
			tail->next = a;
			a = a->next;
		} else {
			tail->next = b;
			b = b->next;
		}
		tail = tail->next;
	}
	tail->next = a?:b;
	return head.next;
}
{%endhighlight%}

The above code will merge two linked list. One extra `struct *tail` is being used to help construct the new linked list.

{%highlight c%}
/**
 * list_sort - sort a list
 * @priv: private data, opaque to list_sort(), passed to @cmp
 * @head: the list to sort
 * @cmp: the elements comparison function
 *
 * This function implements "merge sort", which has O(nlog(n))
 * complexity.
 *
 * The comparison function @cmp must return a negative value if @a
 * should sort before @b, and a positive value if @a should sort after
 * @b. If @a and @b are equivalent, and their original relative
 * ordering is to be preserved, @cmp must return 0.
 */
void list_sort(void *priv, struct list_head *head,
		int (*cmp)(void *priv, struct list_head *a,
			struct list_head *b))
{
	struct list_head *part[MAX_LIST_LENGTH_BITS+1]; /* sorted partial lists
						-- last slot is a sentinel */
	int lev;  /* index into part[] */
	int max_lev = 0;
	struct list_head *list;

	if (list_empty(head))
		return;

	memset(part, 0, sizeof(part));

	head->prev->next = NULL;
	list = head->next;

	while (list) {
		struct list_head *cur = list;
		list = list->next;
		cur->next = NULL;

		for (lev = 0; part[lev]; lev++) {
			cur = merge(priv, cmp, part[lev], cur);
			part[lev] = NULL;
		}
		if (lev > max_lev) {
			if (unlikely(lev >= ARRAY_SIZE(part)-1)) {
				printk_once(KERN_DEBUG "list passed to"
					" list_sort() too long for"
					" efficiency\n");
				lev--;
			}
			max_lev = lev;
		}
		part[lev] = cur;
	}

	for (lev = 0; lev < max_lev; lev++)
		if (part[lev])
			list = merge(priv, cmp, part[lev], list);

	merge_and_restore_back_links(priv, cmp, head, part[max_lev], list);
}
{%endhighlight%}
The above code deals with the merge sort, which is really tricky and amazing.
The key idea is in the 

{%highlight c%}
	struct list_head *part[MAX_LIST_LENGTH_BITS+1]; /* sorted partial lists
						-- last slot is a sentinel */
{%endhighlight%}

This part will store the partial lists, whose lengths whill be 
$$2^i$$ where $$i$$ is the index of the part array.

It uses the [Fibonacci](http://en.wikipedia.org/wiki/Fibonacci_number)
property of the length in the mergesort list. Only the same length list of occur
in the same specific position, and two same length lists will merge to a single list  
whose length will double while the index in the part array is just the next postion.

At last, just merge all the elements in the part array, then the final list is constructed!





