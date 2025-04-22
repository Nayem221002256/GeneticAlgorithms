# GeneticAlgorithms
import random

# Problem size
N = 8
POPULATION_SIZE = 100
GENERATIONS = 1000
MUTATION_RATE = 0.2

# Fitness Function: number of non-attacking pairs
def fitness(chromosome):
    non_attacking = 0
    for i in range(N):
        for j in range(i + 1, N):
            if abs(i - j) != abs(chromosome[i] - chromosome[j]):
                non_attacking += 1
    return non_attacking

# Create initial population
def create_population():
    return [random.sample(range(N), N) for _ in range(POPULATION_SIZE)]

# Tournament selection
def select(population, scores, k=3):
    selected = random.choices(list(zip(population, scores)), k=k)
    selected.sort(key=lambda x: x[1], reverse=True)
    return selected[0][0]

# Order crossover (OX)
def crossover(parent1, parent2):
    a, b = sorted(random.sample(range(N), 2))
    child = [None] * N
    child[a:b] = parent1[a:b]
    p2_filtered = [gene for gene in parent2 if gene not in child]
    ptr = 0
    for i in range(N):
        if child[i] is None:
            child[i] = p2_filtered[ptr]
            ptr += 1
    return child

# Mutation: Swap two positions
def mutate(chromosome):
    if random.random() < MUTATION_RATE:
        i, j = random.sample(range(N), 2)
        chromosome[i], chromosome[j] = chromosome[j], chromosome[i]
    return chromosome

# Run GA
def run_ga():
    population = create_population()
    for generation in range(GENERATIONS):
        scores = [fitness(individual) for individual in population]
        best_score = max(scores)
        best_individual = population[scores.index(best_score)]

        print(f"Generation {generation}, Best Score: {best_score}/{(N*(N-1))//2}")

        if best_score == (N * (N - 1)) // 2:
            print("\n✅ Solution Found:")
            return best_individual

        new_population = []
        for _ in range(POPULATION_SIZE):
            p1 = select(population, scores)
            p2 = select(population, scores)
            child = crossover(p1, p2)
            child = mutate(child)
            new_population.append(child)

        population = new_population
    print("\n❌ No perfect solution found.")
    return best_individual

# Pretty print board
def print_board(solution):
    print("\nChessboard:")
    for row in range(N):
        line = ""
        for col in range(N):
            if solution[row] == col:
                line += " Q"
            else:
                line += " ."
        print(line)

# Run the genetic algorithm
solution = run_ga()
print_board(solution)
