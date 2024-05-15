# xx
sdss
class Graph<T>
    {
        private class Node
        {
            public T value { get; set; }
            public List<Tuple<Node, int>> Successors { get; set; }

            public Node(T value)
            {
                this.value = value;
                this.Successors = new List<Tuple<Node, int>>();
            }

            public override string ToString()
            {
                return value.ToString();
            }
           

           
        }//Class Node
        private List<Node> nodes;
        public int Size
        {
            get
            {
                return nodes.Count;
                }
        }
        public Graph()
        {
            nodes = new List<Node>();
        }

        private Node GetNode(T value)
        {
            return nodes.FirstOrDefault(nde => nde.value.Equals(value));
        }
        public void AddNode(T u)
        {
            Node U = GetNode(u);
            if (U == default) //Not already on the in the list
                nodes.Add(new Node(u));

        }
        public void RemoveNode(T u)
        {
            Node U = GetNode(u);
            if(U != default)
            {
                foreach(Node node in nodes)
                {
                    RemoveSuccessor(node.value, u);
                }

                nodes.Remove(U);
            }
        }

        public void RemoveSuccessor(T u, T v)
        {
            Node U = GetNode(u);
            Node V = GetNode(v);

            if(V != default && U != default)
            {
                Tuple<Node, int> Successors = U.Successors.FirstOrDefault(nde => nde.Item1.Equals(V));
                U.Successors.Remove(Successors);
            }
        }
        public void AddSuccesor(T u, T v, int w)
        {
            Node U = GetNode(u);
            Node V = GetNode(v);

            if (U != default && V != default)
                U.Successors.Add(Tuple.Create(V, w));

            AddNode(u);
            AddNode(v);
            GetNode(u).Successors.Add(new Tuple<Node, int>(GetNode(v), w));
        }
        public bool HasSuccessor(T u, T v)
        {
            Node U = GetNode(u);
            Node V = GetNode(v);

            if(U != default && V !=default)
            {
                return U.Successors.Any(s => s.Item1.Equals(V));

            }
            return false;
        }
        public IList<T> GetSuccessor(T u)
        {
            Node U = GetNode(u);
            if(U !=  default)
            {
                return U.Successors.Select(s => s.Item1.value).ToList();
            }
            return null;
        }
        public List<KeyValuePair<T, int>> GetSuccessorsWithCost(T u)
        {
            Node U = GetNode(u);
            if(U != default)
            {
                return U.Successors.Select(s => new KeyValuePair<T, int>(s.Item1.value, s.Item2)).ToList();
            }
            return null;
        }
        public List<T> ListAllNodesDFT(T u)
        {
            List<T> ListNodes = new List<T>();
            TraverseDFT(ListNodes, u);
            return ListNodes;
        }
        private void TraverseDFT(List<T> lstNodes, T u)
        {
            if(!lstNodes.Contains(u))
            {
                lstNodes.Add(u);
                Node U = GetNode(u);
                foreach (Tuple<Node, int> successor in U.Successors.OrderBy(V => V.Item2))
                    TraverseDFT(lstNodes, successor.Item1.value);
            }
        }
        public IList<T> ListAllNodesBFT(T u)
        {
            List<T> ListNodes = new List<T>();
            foreach (T z in nodes.Select(nde => nde.value))
                ListAllNodesBFT(ListNodes, z);

            return ListNodes;
        }
        private List<T> ListAllNodesBFT(List<T> LstNodes, T u)
        {
            Queue<T> Q = new Queue<T>();
            Q.Enqueue(u);

            while(Q.Count > 0)
            {
                u = Q.Dequeue();
                if(!LstNodes.Contains(u))
                {
                    LstNodes.Add(u);
                    Node U = GetNode(u);
                    if (U != default)
                        foreach (Tuple<Node, int> v in U.Successors.OrderBy(V => V.Item2))
                            Q.Enqueue(v.Item1.value);
                }

            }

            return LstNodes;
        }

