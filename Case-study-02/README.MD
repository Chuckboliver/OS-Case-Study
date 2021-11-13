# Case study 02 : Synchronization

## Version 1 :

- function EnQueue

```sh
static void EnQueue(int eq)
{
    lock(_lock)
    {
        if (Count == 10) // <--- use if
        {
            Console.WriteLine("Queue is full");
        }

        TSBuffer[Back] = eq;
        Back = (Back + 1) % 10;
        Count++;
    }
}
```

- function DeQueue

```sh
static int DeQueue()
{

    lock(_lock)
    {
        if (Count == 0) // <-- use if
        {
            Console.WriteLine("Queue is empty");
        }
        int dq = TSBuffer[Front];
        Front = (Front + 1) % 10;
        Count--;
        return dq;
    }
}
```

- result : Wrong

## Version 2:

- function EnQueue

```sh
static void EnQueue(int eq)
{
    lock (_lock)
    {
        if (Count >= 10)
        {
            Console.WriteLine("Queue is full");
            Monitor.Wait(_lock);
        }

        TSBuffer[Back] = eq;
        Back = (Back + 1) % 10;
        Count++;
        Monitor.PulseAll(_lock);
    }
}
```

- function DeQueue

```sh
static int DeQueue()
{
    lock (_lock)
    {
        if (Count <= 0)
        {
            Console.WriteLine("Queue is empty");
            Monitor.Wait(_lock);
        }
        int x = TSBuffer[Front];
        Front = (Front + 1) % 10;
        Count--;
        Monitor.PulseAll(_lock);
        return x;
    }
}
```

- result : Wrong when use 3 thread as the consumer

## Version 3

- function EnQueue

```sh
static void EnQueue(int eq)
{
    lock (_lock)
    {
        while (Count >= 10)
        {
            Console.WriteLine("Queue is full");
            Monitor.Wait(_lock);
        }

        TSBuffer[Back] = eq;
        Back = (Back + 1) % 10;
        Count++;
        Monitor.PulseAll(_lock);
    }
}
```

- function DeQueue

```sh
static int DeQueue()
{
    lock (_lock)
    {
        while (Count <= 0)
        {
            Console.WriteLine("Queue is empty");
            Monitor.Wait(_lock);
        }
        int x = TSBuffer[Front];
        Front = (Front + 1) % 10;
        Count--;
        Monitor.PulseAll(_lock);
        return x;
    }
}
```

- result : Correct but wrong sometimes(context switching after dequeue complete)
